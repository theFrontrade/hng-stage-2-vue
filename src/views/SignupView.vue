<template>
  <div class="signup-page">
    <div class="signup-container">
      <div class="signup-card">
        <h1>Create Account</h1>
        <form @submit.prevent="handleSignup">
          <div class="form-group">
            <label for="name">Full Name</label>
            <input v-model="form.name" type="text" id="name" placeholder="John Doe" required />
            <span v-if="errors.name" class="error">{{ errors.name }}</span>
          </div>

          <div class="form-group">
            <label for="email">Email</label>
            <input
              v-model="form.email"
              type="email"
              id="email"
              placeholder="john@example.com"
              required
            />
            <span v-if="errors.email" class="error">{{ errors.email }}</span>
          </div>

          <div class="form-group">
            <label for="password">Password</label>
            <input
              v-model="form.password"
              type="password"
              id="password"
              placeholder="••••••••"
              required
            />
            <span v-if="errors.password" class="error">{{ errors.password }}</span>
          </div>

          <div class="form-group">
            <label for="confirm">Confirm Password</label>
            <input
              v-model="form.confirm"
              type="password"
              id="confirm"
              placeholder="••••••••"
              required
            />
            <span v-if="errors.confirm" class="error">{{ errors.confirm }}</span>
          </div>

          <button type="submit" class="submit-button">Sign Up</button>
        </form>

        <p class="login-link">
          Already have an account?
          <button @click="goToLogin" class="link-button">Login</button>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

// --- Interfaces ---
interface SignupForm {
  name: string
  email: string
  password: string
  confirm: string
}

interface FormErrors {
  name?: string
  email?: string
  password?: string
  confirm?: string
}

interface User {
  id: string
  email: string
  name: string
  password?: string
}

// --- Default Admin User (same as in React) ---
const DEFAULT_USER: User = {
  id: 'admin123',
  email: 'sadeniyi016@gmail.com',
  name: 'Admin',
  password: 'password123',
}

const router = useRouter()
const form = ref<SignupForm>({
  name: '',
  email: '',
  password: '',
  confirm: '',
})
const errors = ref<FormErrors>({})
const isLoading = ref(false)
const message = ref<{ type: 'success' | 'error'; text: string } | null>(null)

// --- Helpers for user storage ---
const getStoredUsers = (): User[] => {
  const users = localStorage.getItem('ticketapp_users')
  return users ? JSON.parse(users) : []
}

const saveUsers = (users: User[]) => {
  localStorage.setItem('ticketapp_users', JSON.stringify(users))
}

const saveSession = (user: User) => {
  const { ...safeUser } = user
  localStorage.setItem('ticketapp_session', JSON.stringify(safeUser))
}

// --- Validation ---
const validateForm = (): boolean => {
  errors.value = {}

  if (!form.value.name.trim()) {
    errors.value.name = 'Name is required'
  }

  if (!form.value.email.trim()) {
    errors.value.email = 'Email is required'
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email)) {
    errors.value.email = 'Invalid email format'
  }

  if (!form.value.password) {
    errors.value.password = 'Password is required'
  } else if (form.value.password.length < 6) {
    errors.value.password = 'Password must be at least 6 characters'
  }

  if (form.value.password !== form.value.confirm) {
    errors.value.confirm = 'Passwords do not match'
  }

  return Object.keys(errors.value).length === 0
}

// --- Signup Logic (matches React) ---
const handleSignup = () => {
  if (!validateForm()) return

  isLoading.value = true
  setTimeout(() => {
    const email = form.value.email.trim().toLowerCase()
    const name = form.value.name.trim()
    const password = form.value.password.trim()

    const users = getStoredUsers()

    // 1️⃣ Prevent duplicate users
    const existingUser = users.find((u) => u.email.toLowerCase() === email)
    if (existingUser || email === DEFAULT_USER.email.toLowerCase()) {
      message.value = { type: 'error', text: 'User already exists' }
      finish(false)
      return
    }

    // 2️⃣ Create new user
    const newUser: User = {
      id: Math.random().toString(36).substr(2, 9),
      email,
      name,
      password,
    }

    users.push(newUser)
    saveUsers(users)

    // 3️⃣ Auto-login (save to session)
    saveSession(newUser)

    message.value = { type: 'success', text: 'Account created successfully!' }
    finish(true)
  }, 800)
}

// --- Navigation & helpers ---
const goToLogin = () => {
  router.push('/auth/login')
}

const finish = (redirect: boolean) => {
  setTimeout(() => {
    isLoading.value = false
    if (redirect) router.push('/dashboard')
  }, 1000)
  setTimeout(() => (message.value = null), 3000)
}
</script>

<style scoped>
.signup-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(to bottom right, #eff6ff, #e0e7ff);
  padding: 2rem;
}

.signup-container {
  width: 100%;
  max-width: 450px;
}

.signup-card {
  background: white;
  padding: 2rem;
  border-radius: 8px;
}

.signup-card h1 {
  text-align: center;
  margin-bottom: 2rem;
  color: #1f2937;
  font-size: 1.75rem;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  color: #374151;
  font-weight: 500;
}

.form-group input {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #d1d5db;
  border-radius: 4px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.error {
  display: block;
  color: #ef4444;
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.submit-button {
  width: 100%;
  padding: 0.75rem;
  background-color: #3b82f6;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s;
}

.submit-button:hover {
  background-color: #2563eb;
}

.login-link {
  text-align: center;
  margin-top: 1.5rem;
  color: #6b7280;
}

.link-button {
  background: none;
  border: none;
  color: #3b82f6;
  cursor: pointer;
  text-decoration: underline;
  font-weight: 600;
}

.link-button:hover {
  color: #2563eb;
}

@media (max-width: 480px) {
  .signup-card {
    padding: 1.5rem;
  }

  .signup-card h1 {
    font-size: 1.5rem;
  }
}
</style>
