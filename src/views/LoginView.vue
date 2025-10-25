<template>
  <div class="auth-page">
    <div class="auth-container">
      <div class="auth-card">
        <h2 class="auth-title">Welcome Back</h2>
        <p class="auth-subtitle">Sign in to your TicketHub account</p>

        <form @submit.prevent="handleLogin" class="auth-form">
          <div class="form-group">
            <label for="email">Email Address</label>
            <input
              id="email"
              v-model="form.email"
              type="email"
              placeholder="you@example.com"
              required
              @blur="validateEmail"
            />
            <span v-if="errors.email" class="error-message">{{ errors.email }}</span>
          </div>

          <div class="form-group">
            <label for="password">Password</label>
            <input
              id="password"
              v-model="form.password"
              type="password"
              placeholder="••••••••"
              required
              @blur="validatePassword"
            />
            <span v-if="errors.password" class="error-message">{{ errors.password }}</span>
          </div>

          <button type="submit" class="btn btn-primary btn-block" :disabled="isLoading">
            {{ isLoading ? 'Signing in...' : 'Sign In' }}
          </button>
        </form>

        <p class="auth-footer">
          Don't have an account?
          <router-link to="/auth/signup" class="link">Sign up here</router-link>
        </p>
        <div class="demo-credentials">
          <p class="demo-title">Demo Credentials:</p>
          <p class="demo-text">Email: Sadeniyi016@gmail.com</p>
          <p class="demo-text">Password: password123</p>
        </div>
        <div v-if="message" :class="['toast', message.type]">
          {{ message.text }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

// --- Types ---
interface LoginForm {
  email: string
  password: string
}

interface FormErrors {
  email?: string
  password?: string
}

interface Message {
  type: 'success' | 'error'
  text: string
}

interface User {
  id: string
  email: string
  name: string
  password?: string
}

// --- Default user (same as React AuthProvider) ---
const DEFAULT_USER: User = {
  id: 'admin123',
  email: 'sadeniyi016@gmail.com',
  name: 'Admin',
  password: 'password123',
}

const router = useRouter()
const form = ref<LoginForm>({ email: '', password: '' })
const errors = ref<FormErrors>({})
const isLoading = ref(false)
const message = ref<Message | null>(null)

// --- Helpers for local storage users ---
const getStoredUsers = (): User[] => {
  const users = localStorage.getItem('ticketapp_users')
  return users ? JSON.parse(users) : []
}

const saveSession = (user: User) => {
  const { ...safeUser } = user
  localStorage.setItem('ticketapp_session', JSON.stringify(safeUser))
}

// --- Validation ---
const validateEmail = () => {
  if (!form.value.email) {
    errors.value.email = 'Email is required'
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email)) {
    errors.value.email = 'Please enter a valid email'
  } else {
    errors.value.email = ''
  }
}

const validatePassword = () => {
  if (!form.value.password) {
    errors.value.password = 'Password is required'
  } else if (form.value.password.length < 6) {
    errors.value.password = 'Password must be at least 6 characters'
  } else {
    errors.value.password = ''
  }
}

// --- Login Logic ---
const handleLogin = async () => {
  validateEmail()
  validatePassword()
  if (errors.value.email || errors.value.password) return

  isLoading.value = true

  setTimeout(() => {
    const email = form.value.email.trim().toLowerCase()
    const password = form.value.password.trim()

    // 1️⃣ Default admin user
    if (email === DEFAULT_USER.email.toLowerCase() && password === DEFAULT_USER.password) {
      saveSession(DEFAULT_USER)
      message.value = { type: 'success', text: 'Login successful!' }
      redirectToDashboard()
      return
    }

    // 2️⃣ Check local users
    const users = getStoredUsers()
    const foundUser = users.find((u) => u.email.toLowerCase() === email && u.password === password)

    if (foundUser) {
      saveSession(foundUser)
      message.value = { type: 'success', text: 'Login successful!' }
      redirectToDashboard()
    } else {
      message.value = { type: 'error', text: 'Invalid email or password' }
      resetAfterDelay()
    }
  }, 800)
}

// --- Redirect helper ---
const redirectToDashboard = () => {
  setTimeout(() => {
    isLoading.value = false
    router.push('/dashboard')
  }, 1000)
  setTimeout(() => (message.value = null), 3000)
}

// --- Reset helper ---
const resetAfterDelay = () => {
  isLoading.value = false
  setTimeout(() => (message.value = null), 3000)
}
</script>

<style scoped>
.auth-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(to bottom right, #eff6ff, #e0e7ff);
  padding: 1rem;
}

.auth-container {
  width: 100%;
  max-width: 400px;
}

.auth-card {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 20px 25px rgba(0, 0, 0, 0.15);
}

.auth-title {
  font-size: 1.75rem;
  font-weight: 700;
  color: #1f2937;
  margin-bottom: 0.5rem;
}

.auth-subtitle {
  color: #6b7280;
  margin-bottom: 2rem;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  font-weight: 600;
  color: #1f2937;
  margin-bottom: 0.5rem;
}

.form-group input {
  padding: 0.75rem;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.form-group input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.error-message {
  color: #ef4444;
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.btn-block {
  width: 100%;
}

.btn {
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  font-weight: 600;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-primary {
  background: #3b82f6;
  color: white;
}

.btn-primary:hover:not(:disabled) {
  background: #2563eb;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.auth-footer {
  text-align: center;
  color: #6b7280;
  margin-top: 1.5rem;
}

.link {
  color: #3b82f6;
  text-decoration: none;
  font-weight: 600;
}

.link:hover {
  text-decoration: underline;
}

.toast {
  margin-top: 1rem;
  padding: 1rem;
  border-radius: 8px;
  text-align: center;
  font-weight: 600;
}

.toast.success {
  background: #d1fae5;
  color: #065f46;
}

.toast.error {
  background: #fee2e2;
  color: #991b1b;
}

.demo-credentials {
  margin-top: 1.5rem; /* mt-6 */
  padding: 1rem; /* p-4 */
  background-color: #eff6ff; /* bg-blue-50 */
  border-radius: 0.5rem; /* rounded-lg */
  border: 1px solid #bfdbfe; /* border-blue-200 */
}

.demo-title {
  font-size: 0.875rem; /* text-sm */
  color: #1e3a8a; /* text-blue-900 */
  font-weight: 500; /* font-medium */
  margin-bottom: 0.5rem; /* mb-2 */
}

.demo-text {
  font-size: 0.875rem; /* text-sm */
  color: #1e40af; /* text-blue-800 */
}
</style>
