<template>
  <div class="dashboard-page">
    <header class="dashboard-header">
      <div class="header-content">
        <h1>Dashboard</h1>
        <button @click="handleLogout" class="logout-button">Logout</button>
      </div>
    </header>

    <div class="dashboard-container">
      <nav class="sidebar">
        <button
          @click="navigateTo('/dashboard')"
          class="nav-item"
          :class="{ active: currentRoute === '/dashboard' }"
        >
          Dashboard
        </button>
        <button
          @click="navigateTo('/tickets')"
          class="nav-item"
          :class="{ active: currentRoute === '/tickets' }"
        >
          Tickets
        </button>
      </nav>

      <main class="dashboard-content">
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-icon">📊</div>
            <div class="stat-info">
              <h3>Total Tickets</h3>
              <p class="stat-value">{{ totalTickets }}</p>
            </div>
          </div>

          <div class="stat-card">
            <div class="stat-icon">⏳</div>
            <div class="stat-info">
              <h3>Open Tickets</h3>
              <p class="stat-value">{{ openTickets }}</p>
            </div>
          </div>

          <div class="stat-card">
            <div class="stat-icon">✅</div>
            <div class="stat-info">
              <h3>Resolved Tickets</h3>
              <p class="stat-value">{{ closedTickets }}</p>
            </div>
          </div>
        </div>

        <div class="quick-actions">
          <h2>Quick Actions</h2>
          <button @click="navigateTo('/tickets')" class="action-button">Create New Ticket</button>
          <button @click="navigateTo('/tickets')" class="action-button secondary">
            View All Tickets
          </button>
        </div>

        <div class="recent-tickets">
          <h2>Recent Tickets</h2>
          <div v-if="recentTickets.length > 0" class="tickets-list">
            <div v-for="ticket in recentTickets" :key="ticket.id" class="ticket-item">
              <div class="ticket-header">
                <h4>{{ ticket.title }}</h4>
                <span :class="['priority-badge', ticket.priority.toLowerCase()]">
                  {{ ticket.priority }}
                </span>
              </div>
              <p class="ticket-description">{{ ticket.description }}</p>
              <div class="ticket-footer">
                <span :class="['status-badge', ticket.status.toLowerCase()]">
                  {{ ticket.status }}
                </span>
                <span class="ticket-date">{{ formatDate(ticket.createdAt) }}</span>
              </div>
            </div>
          </div>
          <p v-else class="no-tickets">No tickets yet. Create one to get started!</p>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const currentRoute = computed(() => route.path)

const tickets = ref<
  {
    id: number
    title: string
    description: string
    priority: string
    status: string
    createdAt: string
  }[]
>([])

const totalTickets = computed(() => tickets.value.length)
const openTickets = computed(() => tickets.value.filter((t) => t.status === 'Open').length)
const closedTickets = computed(() => tickets.value.filter((t) => t.status === 'Closed').length)

const recentTickets = computed(() => tickets.value.slice(-3).reverse())

const loadTickets = () => {
  const stored = localStorage.getItem('tickets')
  if (stored) {
    tickets.value = JSON.parse(stored)
  }
}

const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString()
}

const navigateTo = (path: string) => {
  router.push(path)
}

const handleLogout = () => {
  localStorage.removeItem('ticketapp_session')
  router.push('/auth/login')
}

onMounted(() => {
  loadTickets()
})
</script>

<style scoped>
.dashboard-page {
  min-height: 100vh;
  background-color: #f9fafb;
}

.dashboard-header {
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

.dashboard-header h1 {
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

.dashboard-container {
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

.dashboard-content {
  flex: 1;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.stat-card {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
  display: flex;
  gap: 1rem;
  align-items: center;
  transition: box-shadow 0.3s;
}

.stat-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-icon {
  font-size: 2rem;
}

.stat-info h3 {
  font-size: 0.875rem;
  color: #6b7280;
  margin-bottom: 0.5rem;
  font-weight: 500;
}

.stat-value {
  font-size: 1.875rem;
  font-weight: bold;
  color: #1f2937;
}

.quick-actions {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
  margin-bottom: 2rem;
}

.quick-actions h2 {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  color: #1f2937;
}

.action-button {
  background-color: #3b82f6;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  margin-right: 1rem;
  transition: background-color 0.3s;
}

.action-button:hover {
  background-color: #2563eb;
}

.action-button.secondary {
  background-color: #e5e7eb;
  color: #1f2937;
}

.action-button.secondary:hover {
  background-color: #d1d5db;
}

.recent-tickets {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
}

.recent-tickets h2 {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  color: #1f2937;
}

.tickets-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.ticket-item {
  border: 1px solid #e5e7eb;
  padding: 1rem;
  border-radius: 4px;
  transition: box-shadow 0.3s;
}

.ticket-item:hover {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.ticket-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5rem;
}

.ticket-header h4 {
  color: #1f2937;
  font-size: 1rem;
}

.priority-badge {
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

.ticket-description {
  color: #6b7280;
  font-size: 0.875rem;
  margin-bottom: 0.75rem;
}

.ticket-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.status-badge {
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.75rem;
  font-weight: 600;
}

.status-badge.open {
  background-color: #dbeafe;
  color: #0c2d6b;
}

.status-badge.closed {
  background-color: #dcfce7;
  color: #166534;
}

.ticket-date {
  color: #9ca3af;
  font-size: 0.875rem;
}

.no-tickets {
  text-align: center;
  color: #9ca3af;
  padding: 2rem;
}

@media (max-width: 768px) {
  .dashboard-container {
    flex-direction: column;
  }

  .sidebar {
    width: 100%;
    flex-direction: row;
  }

  .stats-grid {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  }
}
</style>
