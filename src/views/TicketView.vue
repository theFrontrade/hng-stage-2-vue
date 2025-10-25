<template>
  <div class="tickets-page">
    <header class="tickets-header">
      <div class="header-content">
        <h1>Tickets</h1>
        <button @click="handleLogout" class="logout-button">Logout</button>
      </div>
    </header>

    <div class="tickets-container">
      <!-- Sidebar Navigation -->
      <nav class="sidebar">
        <button @click="navigateTo('dashboard')" class="nav-item">Dashboard</button>
        <button @click="navigateTo('tickets')" class="nav-item active">Tickets</button>
      </nav>

      <!-- Main Content -->
      <main class="tickets-content">
        <!-- Toolbar -->
        <div class="tickets-toolbar">
          <h2>Manage Tickets</h2>
          <button @click="showCreateForm = !showCreateForm" class="create-button">
            {{ showCreateForm ? 'Cancel' : 'Create New Ticket' }}
          </button>
        </div>

        <!-- Create Ticket Form -->
        <div v-if="showCreateForm" class="create-form">
          <h3>Create New Ticket</h3>
          <form @submit.prevent="handleCreateTicket">
            <div class="form-group">
              <label for="title">Title</label>
              <input
                v-model="newTicket.title"
                type="text"
                id="title"
                placeholder="Ticket title"
                required
              />
              <span v-if="errors.title" class="error">{{ errors.title }}</span>
            </div>

            <div class="form-group">
              <label for="description">Description</label>
              <textarea
                v-model="newTicket.description"
                id="description"
                placeholder="Ticket description"
                rows="4"
                required
              ></textarea>
              <span v-if="errors.description" class="error">{{ errors.description }}</span>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label for="priority">Priority</label>
                <select v-model="newTicket.priority" id="priority" required>
                  <option value="Low">Low</option>
                  <option value="Medium">Medium</option>
                  <option value="High">High</option>
                </select>
              </div>

              <div class="form-group">
                <label for="status">Status</label>
                <select v-model="newTicket.status" id="status" required>
                  <option value="Open">Open</option>
                  <option value="In Progress">In Progress</option>
                  <option value="Closed">Closed</option>
                </select>
              </div>
            </div>

            <button type="submit" class="submit-button">Create Ticket</button>
          </form>
        </div>

        <!-- Ticket List -->
        <div class="tickets-list">
          <h3>All Tickets ({{ tickets.length }})</h3>
          <div v-if="tickets.length > 0" class="tickets-grid">
            <div v-for="ticket in tickets" :key="ticket.id" class="ticket-card">
              <div class="ticket-header">
                <h4>{{ ticket.title }}</h4>
                <div class="ticket-badges">
                  <span :class="['priority-badge', ticket.priority.toLowerCase()]">
                    {{ ticket.priority }}
                  </span>
                  <span :class="['status-badge', ticket.status.toLowerCase().replace(' ', '-')]">
                    {{ ticket.status }}
                  </span>
                </div>
              </div>

              <p class="ticket-description">{{ ticket.description }}</p>
              <div class="ticket-date">Created: {{ formatDate(ticket.createdAt) }}</div>

              <div class="ticket-actions">
                <button @click="editTicket(ticket)" class="edit-button">Edit</button>
                <button @click="deleteTicket(ticket.id)" class="delete-button">Delete</button>
              </div>
            </div>
          </div>
          <p v-else class="no-tickets">No tickets yet. Create one to get started!</p>
        </div>

        <!-- Edit Ticket Form -->
        <div v-if="editingTicket" class="edit-form">
          <h3>Edit Ticket</h3>
          <form @submit.prevent="handleUpdateTicket">
            <div class="form-group">
              <label for="edit-title">Title</label>
              <input v-model="editingTicket.title" type="text" id="edit-title" required />
            </div>

            <div class="form-group">
              <label for="edit-description">Description</label>
              <textarea
                v-model="editingTicket.description"
                id="edit-description"
                rows="4"
                required
              ></textarea>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label for="edit-priority">Priority</label>
                <select v-model="editingTicket.priority" id="edit-priority" required>
                  <option value="Low">Low</option>
                  <option value="Medium">Medium</option>
                  <option value="High">High</option>
                </select>
              </div>

              <div class="form-group">
                <label for="edit-status">Status</label>
                <select v-model="editingTicket.status" id="edit-status" required>
                  <option value="Open">Open</option>
                  <option value="In Progress">In Progress</option>
                  <option value="Closed">Closed</option>
                </select>
              </div>
            </div>

            <div class="form-actions">
              <button type="submit" class="submit-button">Update Ticket</button>
              <button type="button" @click="editingTicket = null" class="cancel-button">
                Cancel
              </button>
            </div>
          </form>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

interface Ticket {
  id: number
  title: string
  description: string
  priority: string
  status: string
  createdAt: string
}

const router = useRouter()
const tickets = ref<Ticket[]>([])
const showCreateForm = ref(false)
const editingTicket = ref<Ticket | null>(null)
const errors = ref<Record<string, string>>({})

const newTicket = ref({
  title: '',
  description: '',
  priority: 'Medium',
  status: 'Open',
})

const loadTickets = () => {
  const stored = localStorage.getItem('tickets')
  if (stored) tickets.value = JSON.parse(stored)
}

const saveTickets = () => {
  localStorage.setItem('tickets', JSON.stringify(tickets.value))
}

const validateTicket = (ticket: { title: string; description: string }) => {
  errors.value = {}

  if (!ticket.title.trim()) errors.value.title = 'Title is required'
  if (!ticket.description.trim()) errors.value.description = 'Description is required'

  return Object.keys(errors.value).length === 0
}

const handleCreateTicket = () => {
  if (validateTicket(newTicket.value)) {
    const ticket: Ticket = {
      id: Date.now(),
      ...newTicket.value,
      createdAt: new Date().toISOString(),
    }

    tickets.value.push(ticket)
    saveTickets()

    newTicket.value = { title: '', description: '', priority: 'Medium', status: 'Open' }
    showCreateForm.value = false
  }
}

const editTicket = (ticket: Ticket) => {
  editingTicket.value = { ...ticket }
}

const handleUpdateTicket = () => {
  if (editingTicket.value && validateTicket(editingTicket.value)) {
    const index = tickets.value.findIndex((t) => t.id === editingTicket.value?.id)
    if (index !== -1) {
      tickets.value[index] = editingTicket.value
      saveTickets()
      editingTicket.value = null
    }
  }
}

const deleteTicket = (id: number) => {
  if (confirm('Are you sure you want to delete this ticket?')) {
    tickets.value = tickets.value.filter((t) => t.id !== id)
    saveTickets()
  }
}

const formatDate = (date: string) => new Date(date).toLocaleDateString()

const navigateTo = (page: string) => {
  if (page === 'dashboard') router.push('/dashboard')
  if (page === 'tickets') router.push('/tickets')
}

const handleLogout = () => {
  localStorage.removeItem('ticketapp_session')
  router.push('/auth/login')
}

onMounted(loadTickets)
</script>

<style scoped>
.tickets-page {
  min-height: 100vh;
  background-color: #f9fafb;
}

.tickets-header {
  background-color: white;
  border-bottom: 1px solid #e5e7eb;
  padding: 1.5rem 2rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.header-content {
  max-width: 1440px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.tickets-header h1 {
  font-size: 1.875rem;
  color: #1f2937;
}

.logout-button {
  background-color: #ef4444;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.3s;
}

.logout-button:hover {
  background-color: #dc2626;
}

.tickets-container {
  max-width: 1440px;
  margin: 0 auto;
  display: flex;
  gap: 2rem;
  padding: 2rem;
}

.sidebar {
  width: 200px;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.nav-item {
  background: white;
  border: 1px solid #e5e7eb;
  padding: 0.75rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  text-align: left;
  transition: all 0.3s;
  font-weight: 500;
  color: #6b7280;
}

.nav-item:hover {
  background-color: #f3f4f6;
  color: #3b82f6;
}

.nav-item.active {
  background-color: #3b82f6;
  color: white;
  border-color: #3b82f6;
}

.tickets-content {
  flex: 1;
}

.tickets-toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.tickets-toolbar h2 {
  font-size: 1.5rem;
  color: #1f2937;
}

.create-button {
  background-color: #3b82f6;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.3s;
}

.create-button:hover {
  background-color: #2563eb;
}

.create-form,
.edit-form {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
  margin-bottom: 2rem;
}

.create-form h3,
.edit-form h3 {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  color: #1f2937;
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  color: #374151;
  font-weight: 500;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #d1d5db;
  border-radius: 4px;
  font-size: 1rem;
  font-family: inherit;
  transition: border-color 0.3s;
}

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.error {
  display: block;
  color: #ef4444;
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.submit-button {
  background-color: #3b82f6;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.3s;
}

.submit-button:hover {
  background-color: #2563eb;
}

.cancel-button {
  background-color: #e5e7eb;
  color: #1f2937;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.3s;
  margin-left: 0.5rem;
}

.cancel-button:hover {
  background-color: #d1d5db;
}

.form-actions {
  display: flex;
  gap: 0.5rem;
}

.tickets-list {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
}

.tickets-list h3 {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  color: #1f2937;
}

.tickets-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
}

.ticket-card {
  border: 1px solid #e5e7eb;
  padding: 1.5rem;
  border-radius: 8px;
  transition: box-shadow 0.3s;
}

.ticket-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.ticket-header {
  margin-bottom: 1rem;
}

.ticket-header h4 {
  color: #1f2937;
  font-size: 1.125rem;
  margin-bottom: 0.5rem;
}

.ticket-badges {
  display: flex;
  gap: 0.5rem;
}

.priority-badge,
.status-badge {
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.75rem;
  font-weight: 600;
}

.priority-badge.high {
  background-color: #fee2e2;
  color: #991b1b;
}

.priority-badge.medium {
  background-color: #fef3c7;
  color: #92400e;
}

.priority-badge.low {
  background-color: #dbeafe;
  color: #0c2d6b;
}

.status-badge.open {
  background-color: #dbeafe;
  color: #0c2d6b;
}

.status-badge.in-progress {
  background-color: #fef3c7;
  color: #92400e;
}

.status-badge.closed {
  background-color: #dcfce7;
  color: #166534;
}

.ticket-description {
  color: #6b7280;
  font-size: 0.875rem;
  margin-bottom: 1rem;
  line-height: 1.5;
}

.ticket-date {
  display: block;
  color: #9ca3af;
  font-size: 0.75rem;
  margin-bottom: 1rem;
}

.ticket-actions {
  display: flex;
  gap: 0.5rem;
}

.edit-button,
.delete-button {
  flex: 1;
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.875rem;
  transition: background-color 0.3s;
}

.edit-button {
  background-color: #3b82f6;
  color: white;
}

.edit-button:hover {
  background-color: #2563eb;
}

.delete-button {
  background-color: #ef4444;
  color: white;
}

.delete-button:hover {
  background-color: #dc2626;
}

.no-tickets {
  text-align: center;
  color: #9ca3af;
  padding: 2rem;
}

@media (max-width: 768px) {
  .tickets-container {
    flex-direction: column;
  }

  .sidebar {
    width: 100%;
    flex-direction: row;
  }

  .tickets-toolbar {
    flex-direction: column;
    gap: 1rem;
    align-items: flex-start;
  }

  .form-row {
    grid-template-columns: 1fr;
  }

  .tickets-grid {
    grid-template-columns: 1fr;
  }
}
</style>
