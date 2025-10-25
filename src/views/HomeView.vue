<template>
  <div class="landing-page">
    <!-- Navigation -->
    <nav class="navbar">
      <div class="container">
        <div class="nav-content">
          <h1 class="logo">TicketHub</h1>
          <div class="nav-links">
            <router-link to="/auth/login" class="btn btn-secondary">Login</router-link>
            <router-link to="/auth/signup" class="btn btn-primary">Get Started</router-link>
          </div>
        </div>
      </div>
    </nav>

    <!-- Hero Section -->
    <section class="hero">
      <svg class="wave" viewBox="0 0 1440 320" preserveAspectRatio="none">
        <path
          fill="#3b82f61a"
          fillOpacity="0.5"
          d="M0,96L48,112C96,128,192,160,288,160C384,160,480,128,576,122.7C672,117,768,139,864,144C960,149,1056,139,1152,128C1248,117,1344,107,1392,101.3L1440,96L1440,320L1392,320C1344,320,1248,320,1152,320C1056,320,960,320,864,320C768,320,672,320,576,320C480,320,384,320,288,320C192,320,96,320,48,320L0,320Z"
        ></path>
      </svg>
      <div class="hero-circle circle-1"></div>
      <div class="container hero-content">
        <div class="hero-wrap">
          <h2 class="hero-title">Manage Your Tickets Effortlessly</h2>
          <p class="hero-subtitle">
            A modern ticket management system designed for teams. Create, track, and resolve tickets
            with ease.
          </p>
          <div class="hero-buttons">
            <router-link to="/auth/signup" class="btn btn-primary btn-lg"
              >Start Free Trial</router-link
            >
            <router-link to="/auth/login" class="btn btn-outline btn-lg">Sign In</router-link>
          </div>
        </div>
      </div>
    </section>

    <!-- Features Section -->
    <section class="features-section">
      <div class="container">
        <h2 class="section-title">Powerful Features</h2>
        <div class="features-grid">
          <div v-for="(feature, idx) in features" :key="idx" class="feature-card">
            <div class="feature-icon">{{ feature.icon }}</div>
            <h3 class="feature-title">{{ feature.title }}</h3>
            <p class="feature-description">{{ feature.description }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- CTA Section -->
    <section class="cta-section">
      <div class="container text-center">
        <h2 class="section-title">Ready to Get Started?</h2>
        <p class="cta-text">
          Join thousands of teams using TicketHub to manage their workflow efficiently.
        </p>
        <router-link to="/auth/signup" class="btn-primary"> Create Your Account </router-link>
      </div>
    </section>

    <!-- Footer -->
    <footer class="footer">
      <div class="container">
        <div class="footer-grid">
          <div>
            <h4 class="footer-title">TicketHub</h4>
            <p class="footer-text">Modern ticket management for teams.</p>
          </div>

          <div>
            <h4 class="footer-title">Product</h4>
            <ul class="footer-links">
              <li><a href="#">Features</a></li>
              <li><a href="#">Pricing</a></li>
              <li><a href="#">Security</a></li>
            </ul>
          </div>

          <div>
            <h4 class="footer-title">Company</h4>
            <ul class="footer-links">
              <li><a href="#">About</a></li>
              <li><a href="#">Blog</a></li>
              <li><a href="#">Contact</a></li>
            </ul>
          </div>

          <div>
            <h4 class="footer-title">Legal</h4>
            <ul class="footer-links">
              <li><a href="#">Privacy</a></li>
              <li><a href="#">Terms</a></li>
              <li><a href="#">Cookies</a></li>
            </ul>
          </div>
        </div>

        <div class="footer-bottom">
          <p>&copy; 2025 TicketHub. All rights reserved.</p>
        </div>
      </div>
    </footer>

    <!-- Footer -->
    <footer class="footer">
      <div class="container">
        <p>&copy; 2025 TicketHub. All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
const features = [
  {
    title: 'Easy Ticket Creation',
    description: 'Create and organize tickets with custom fields and priorities.',
    icon: '📝',
  },
  {
    title: 'Real-time Updates',
    description: 'Track ticket status changes and get instant notifications.',
    icon: '⚡',
  },
  {
    title: 'Team Collaboration',
    description: 'Work together with your team to resolve issues faster.',
    icon: '👥',
  },
]
</script>

<style scoped>
.landing-page {
  min-height: 100vh;
  background: white;
}

.navbar {
  background: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
}

.nav-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
}

.logo {
  font-size: 1.5rem;
  font-weight: 700;
  color: #3b82f6;
  margin: 0;
}

.nav-links {
  display: flex;
  gap: 1rem;
  align-items: center;
}

.hero {
  position: relative;
  padding: 6rem 0 4rem;
  background: transparent;
  color: white;
  overflow: hidden;
  height: 50vh;
}

.wave {
  position: absolute;
  bottom: -1px;
  left: 0;
  width: 100%;
  height: 400px;
}

.hero-circle {
  position: absolute;
  width: 300px;
  height: 300px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  top: -100px;
  right: -100px;
}

.circle-1 {
  width: 250px;
  height: 250px;
  top: 50px;
  right: 10%;
}

.hero-content {
  position: relative;
  z-index: 10;
  text-align: start;
  max-width: 740px;
}

.hero-title {
  font-size: 3rem;
  font-weight: 700;
  margin-bottom: 1rem;
  line-height: 1.2;
  color: black;
}

.hero-subtitle {
  font-size: 1.25rem;
  margin-bottom: 2rem;
  opacity: 0.95;
  color: black;
}

.hero-buttons {
  display: flex;
  gap: 1rem;
  justify-content: start;
  flex-wrap: wrap;
}

.features {
  position: relative;
  padding: 4rem 0;
  background: #f9fafb;
}

.circle-2 {
  position: absolute;
  width: 200px;
  height: 200px;
  background: rgba(59, 130, 246, 0.1);
  border-radius: 50%;
  bottom: 10%;
  left: 5%;
}

.section-title {
  text-align: center;
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 3rem;
  color: #1f2937;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
  position: relative;
  z-index: 10;
}

.feature-card {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease;
}

.feature-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 16px rgba(0, 0, 0, 0.15);
}

.feature-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.feature-card h4 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
  color: #1f2937;
}

.feature-card p {
  color: #6b7280;
  line-height: 1.6;
}

.footer {
  background: #1f2937;
  color: white;
  padding: 2rem 0;
  text-align: center;
}

.container {
  max-width: 1440px;
  margin: 0 auto;
  padding: 0 1rem;
}

.hero-wrap {
  max-width: 720px;
}

.btn {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
  border: 2px solid transparent;
  cursor: pointer;
}

.btn-primary {
  background: #3b82f6;
  color: white;
}

.btn-primary:hover {
  background: #2563eb;
}

.btn-secondary {
  color: #3b82f6;
  border-color: #3b82f6;
}

.btn-secondary:hover {
  background: #f0f9ff;
}

.btn-outline {
  border-color: #2563eb;
  color: #2563eb;
}

.btn-outline:hover {
  background: rgba(255, 255, 255, 0.1);
}

.btn-lg {
  padding: 1rem 2rem;
  font-size: 1.1rem;
}

/* ---------- Features Section ---------- */
.features-section {
  padding: 5rem 0;
  background-color: #f9fafb;
}

.section-title {
  font-size: 2.25rem;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
}

.features-grid {
  display: grid;
  gap: 2rem;
  grid-template-columns: 1fr;
}

@media (min-width: 768px) {
  .features-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

.feature-card {
  background-color: white;
  border-radius: 12px;
  padding: 2rem;
  text-align: center;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
}

.feature-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.08);
}

.feature-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.feature-title {
  font-size: 1.25rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.feature-description {
  color: #6b7280;
  font-size: 0.95rem;
}

/* ---------- CTA Section ---------- */
.cta-section {
  padding: 5rem 0;
  background: white;
  color: black;
  text-align: center;
}

.cta-text {
  font-size: 1.125rem;
  max-width: 600px;
  margin: 1rem auto 2rem;
  color: #6b7280;
}

.btn-primary {
  display: inline-block;
  padding: 0.75rem 1.75rem;
  font-size: 1rem;
  font-weight: 600;
  color: white;
  background-color: #1e40af;
  border-radius: 8px;
  text-decoration: none;
  transition: background-color 0.2s ease;
}

.btn-primary:hover {
  background-color: #1d4ed8;
}

/* ---------- Footer ---------- */
.footer {
  background-color: #f9fafb;
  border-top: 1px solid #e5e7eb;
  padding: 3rem 0;
}

.footer-grid {
  display: grid;
  gap: 2rem;
  grid-template-columns: 1fr;
  margin-bottom: 2rem;
}

@media (min-width: 768px) {
  .footer-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

.footer-title {
  font-weight: 700;
  margin-bottom: 1rem;
  color: #111827;
}

.footer-text {
  color: #6b7280;
  font-size: 0.9rem;
}

.footer-links {
  list-style: none;
  padding: 0;
  margin: 0;
}

.footer-links li {
  margin-bottom: 0.5rem;
}

.footer-links a {
  color: #6b7280;
  font-size: 0.9rem;
  text-decoration: none;
  transition: color 0.2s ease;
}

.footer-links a:hover {
  color: #3b82f6;
}

.footer-bottom {
  border-top: 1px solid #e5e7eb;
  padding-top: 1.5rem;
  text-align: center;
  font-size: 0.9rem;
  color: #6b7280;
}
@media (max-width: 768px) {
  .nav-links {
    flex-direction: column;
    gap: 0.5rem;
  }

  .hero-title {
    font-size: 2rem;
  }

  .hero-subtitle {
    font-size: 1rem;
  }

  .hero-buttons {
    flex-direction: column;
  }

  .btn {
    width: 100%;
    text-align: center;
  }

  .features-grid {
    grid-template-columns: 1fr;
  }
}
</style>
