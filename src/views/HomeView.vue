<script setup>
import { ref, onMounted, onUnmounted, watch, provide } from 'vue'
import ProjectCard from '../components/ProjectCard.vue'
import SkillsSection from '../components/SkillsSection.vue'
import '../assets/styles.css'

const isVisible = ref(false)
const isDarkMode = ref(true)

provide('isDarkMode', isDarkMode)

// ── Click particles ──
const clickParticles = ref([])
let particleId = 0

const handlePageClick = (e) => {
  // Ignore clicks on interactive elements
  if (e.target.closest('button') || e.target.closest('a') || e.target.closest('input')) return

  const colors = ['#39ff14', '#00ffff', '#ff00ff', '#ffff00', '#ff4444']
  for (let i = 0; i < 6; i++) {
    const angle = (Math.PI * 2 * i) / 6
    const speed = 30 + Math.random() * 50
    const id = particleId++
    clickParticles.value.push({
      id,
      x: e.clientX,
      y: e.clientY,
      vx: Math.cos(angle) * speed,
      vy: Math.sin(angle) * speed,
      color: colors[Math.floor(Math.random() * colors.length)],
      size: 4 + Math.random() * 6,
    })
    setTimeout(() => {
      clickParticles.value = clickParticles.value.filter(p => p.id !== id)
    }, 600)
  }
}

// ── Draggable snippet state ──
const snippet1Position = ref({ x: 0, y: 0 })
const snippet2Position = ref({ x: 0, y: 0 })
const isDraggingSnippet1 = ref(false)
const isDraggingSnippet2 = ref(false)
const dragStart = ref({ x: 0, y: 0 })

const startDragSnippet1 = (e) => {
  isDraggingSnippet1.value = true
  const clientX = e.type === 'touchstart' ? e.touches[0].clientX : e.clientX
  const clientY = e.type === 'touchstart' ? e.touches[0].clientY : e.clientY
  dragStart.value = {
    x: clientX - snippet1Position.value.x,
    y: clientY - snippet1Position.value.y
  }
  e.preventDefault()
}

const startDragSnippet2 = (e) => {
  isDraggingSnippet2.value = true
  const clientX = e.type === 'touchstart' ? e.touches[0].clientX : e.clientX
  const clientY = e.type === 'touchstart' ? e.touches[0].clientY : e.clientY
  dragStart.value = {
    x: clientX - snippet2Position.value.x,
    y: clientY - snippet2Position.value.y
  }
  e.preventDefault()
}

const handleDrag = (e) => {
  const clientX = e.type === 'touchmove' ? e.touches[0].clientX : e.clientX
  const clientY = e.type === 'touchmove' ? e.touches[0].clientY : e.clientY

  if (isDraggingSnippet1.value) {
    snippet1Position.value = {
      x: clientX - dragStart.value.x,
      y: clientY - dragStart.value.y
    }
  }
  if (isDraggingSnippet2.value) {
    snippet2Position.value = {
      x: clientX - dragStart.value.x,
      y: clientY - dragStart.value.y
    }
  }
}

const stopDrag = () => {
  if (isDraggingSnippet1.value) {
    isDraggingSnippet1.value = false
    snippet1Position.value = { x: 0, y: 0 }
  }
  if (isDraggingSnippet2.value) {
    isDraggingSnippet2.value = false
    snippet2Position.value = { x: 0, y: 0 }
  }
}

onMounted(() => {
  setTimeout(() => {
    isVisible.value = true
  }, 100)

  const savedTheme = localStorage.getItem('theme')
  if (savedTheme) {
    isDarkMode.value = savedTheme === 'dark'
  } else {
    isDarkMode.value = window.matchMedia('(prefers-color-scheme: dark)').matches
  }

  applyTheme()

  window.addEventListener('mousemove', handleDrag)
  window.addEventListener('mouseup', stopDrag)
  window.addEventListener('touchmove', handleDrag)
  window.addEventListener('touchend', stopDrag)
  window.addEventListener('click', handlePageClick)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleDrag)
  window.removeEventListener('mouseup', stopDrag)
  window.removeEventListener('touchmove', handleDrag)
  window.removeEventListener('touchend', stopDrag)
  window.removeEventListener('click', handlePageClick)
})

watch(isDarkMode, () => {
  applyTheme()
})

const applyTheme = () => {
  document.documentElement.setAttribute('data-theme', isDarkMode.value ? 'dark' : 'light')
  document.body.style.backgroundColor = isDarkMode.value ? '#0a0a0f' : '#f0faf0'
  document.body.style.color = isDarkMode.value ? '#e0ffe0' : '#1a2e1a'
}

const scrollToSection = (sectionId) => {
  const element = document.getElementById(sectionId)
  if (element) {
    element.scrollIntoView({ behavior: 'smooth' })
  }
}

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
  localStorage.setItem('theme', isDarkMode.value ? 'dark' : 'light')
}

const skills = ref([
  { name: 'Vue.js', icon: 'fab fa-vuejs', color: '#42b883', level: 95 },
  { name: 'React', icon: 'fab fa-react', color: '#61dafb', level: 90 },
  { name: 'Node.js', icon: 'fab fa-node-js', color: '#68a063', level: 88 },
  { name: 'Python', icon: 'fab fa-python', color: '#3776ab', level: 85 },
  { name: 'TypeScript', icon: 'fab fa-js-square', color: '#3178c6', level: 92 },
  { name: 'Docker', icon: 'fab fa-docker', color: '#2496ed', level: 80 },
  { name: 'MongoDB', icon: 'fas fa-sql', color: '#47a248', level: 87 },
  { name: 'Git', icon: 'fab fa-git-alt', color: '#f05032', level: 93 }
])

const handleSkillsReorder = ({ from, to, skills: newSkills }) => {
  localStorage.setItem('skillsOrder', JSON.stringify(newSkills.map(s => s.name)))
}

const projects = [
  {
    title: 'StarTech E-Commerce',
    description: 'E-comerce platform where users can buy tech products with just a few clicks.',
    image: 'https://images.unsplash.com/photo-1557821552-17105176677c?w=500&h=300&fit=crop',
    tags: ['Vue.js', 'Rust', 'MongoDB'],
    github: 'https://github.com/NxaSenpai/StarTech',
    demo: 'https://demo.com'
  },
  {
    title: 'Task Management App',
    description: 'Real-time collaborative task manager with chat features, drag-and-drop functionality, and team workspaces.',
    image: 'https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?w=500&h=300&fit=crop',
    tags: ['React', 'Socket.io', 'Express'],
    github: 'https://github.com',
    demo: 'https://demo.com'
  },
  {
    title: 'Release the Card',
    description: 'A card game where players must release three cards at a time to clear the deck.',
    image: 'https://images.unsplash.com/photo-1551288049-bebda4e38f71?w=500&h=300&fit=crop',
    tags: ['Game', 'GodotEngine', 'GDScript'],
    github: 'https://github.com',
    demo: 'https://demo.com'
  }
]

const handleProjectView = (project) => {
  if (project.demo) {
    window.open(project.demo, '_blank')
  }
}

const handleProjectCode = (project) => {
  if (project.github) {
    window.open(project.github, '_blank')
  }
}

const isMobileMenuOpen = ref(false)

const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value
}

const closeMobileMenu = () => {
  isMobileMenuOpen.value = false
}
</script>

<template>
  <div class="home" :class="isDarkMode ? 'dark-mode' : 'light-mode'">
    <!-- Animated Background -->
    <div class="animated-bg">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
    </div>

    <!-- Click particles container -->
    <div class="particles-container">
      <div
        v-for="p in clickParticles"
        :key="p.id"
        class="click-particle"
        :style="{
          left: p.x + 'px',
          top: p.y + 'px',
          '--vx': p.vx + 'px',
          '--vy': p.vy + 'px',
          '--size': p.size + 'px',
          background: p.color,
        }"
      ></div>
    </div>

    <!-- Theme Toggle -->
    <button class="theme-toggle" @click="toggleTheme" aria-label="Toggle theme">
      <i :class="isDarkMode ? 'fas fa-sun' : 'fas fa-moon'"></i>
    </button>

    <!-- Navigation -->
    <nav class="navbar">
      <div class="nav-container">
        <div class="logo">
          <span class="logo-bracket">&lt;</span>
          <span>TANG</span><span class="accent"> Nakry</span>
          <span class="logo-bracket">/&gt;</span>
        </div>

        <button class="mobile-menu-btn" @click="toggleMobileMenu" aria-label="Toggle menu">
          <span :class="{ 'active': isMobileMenuOpen }"></span>
        </button>

        <ul class="nav-links" :class="{ 'active': isMobileMenuOpen }">
          <li><a href="#home" @click="closeMobileMenu"><span class="nav-number">STAGE 1</span>Home</a></li>
          <li><a href="#about" @click="closeMobileMenu"><span class="nav-number">STAGE 2</span>About</a></li>
          <li><a href="#skills" @click="closeMobileMenu"><span class="nav-number">STAGE 3</span>Skills</a></li>
          <li><a href="#projects" @click="closeMobileMenu"><span class="nav-number">STAGE 4</span>Projects</a></li>
          <li><a href="#contact" @click="closeMobileMenu"><span class="nav-number">STAGE 5</span>Contact</a></li>
          <li>
            <a href="/resume.pdf" target="_blank" class="resume-btn">
            <i class="fa-solid fa-circle-down"></i> Resume
            </a>
          </li>
        </ul>
      </div>
    </nav>

    <!-- Hero — STAGE 1 -->
    <section class="hero" id="home" :class="{ 'fade-in': isVisible }">
      <div class="hero-content">
        <div class="text-content">
          <div class="greeting">
            Hello, I'm
          </div>
          <h1 class="title">
            TANG <span class="gradient-text">Nakry</span>
          </h1>
          <h2 class="subtitle">
            <span class="typing-container">
              <span class="typing-text">Web & App Developer</span>
            </span>
          </h2>
          <div class="dialogue-box">
            <div class="dialogue-name">NAKRY</div>
            <p class="description">
              Hello! I'm Nakry, just a guy who loves coding, living in Cambodia.
              I enjoy creating things that live on the internet — websites,
              applications, or anything in between. Also games!
            </p>
            <div class="dialogue-prompt">▼ PRESS A TO CONTINUE</div>
          </div>

          <div class="stats">
            <div class="stat-item">
              <div class="stat-number">3</div>
              <div class="stat-label">Projects</div>
            </div>
            <div class="stat-item">
              <div class="stat-number">0</div>
              <div class="stat-label">Years Exp.</div>
            </div>
            <div class="stat-item">
              <div class="stat-number">8</div>
              <div class="stat-label">Technologies</div>
            </div>
          </div>

          <div class="cta-buttons">
            <button class="btn btn-primary" @click="scrollToSection('projects')">
              <span>▶ View Projects</span>
              <div class="btn-shine"></div>
            </button>
            <button class="btn btn-secondary" @click="scrollToSection('contact')">
              <span>✉ Contact Me</span>
              <i class="fas fa-envelope"></i>
            </button>
          </div>

          <div class="social-links">
            <a href="https://github.com/NxaSenpai" target="_blank" class="social-icon" aria-label="GitHub">
              <i class="fab fa-github"></i>
            </a>
            <a href="https://linkedin.com" target="_blank" class="social-icon" aria-label="LinkedIn">
              <i class="fab fa-linkedin"></i>
            </a>
            <a href="https://youtube.com" target="_blank" class="social-icon" aria-label="Telegram">
              <i class="fa-brands fa-telegram"></i>
            </a>
            <a href="mailto:your@email.com" class="social-icon" aria-label="Email">
              <i class="fas fa-envelope"></i>
            </a>
          </div>
        </div>

        <div class="image-content">
          <div class="profile-wrapper">
            <div class="glow-effect"></div>
            <div class="profile-image">
              <img style="opacity: 0.6;" src="/my_pic.jpg" alt="Profile" />
              <div class="image-overlay"></div>
              <div class="profile-hp-bar">
                <div class="hp-fill"></div>
                <span class="hp-text">HP</span>
              </div>
            </div>
            <div
              class="code-snippet snippet-1"
              :class="{ 'dragging': isDraggingSnippet1, 'returning': !isDraggingSnippet1 }"
              :style="{
                transform: `translate(${snippet1Position.x}px, ${snippet1Position.y}px)`,
                cursor: isDraggingSnippet1 ? 'grabbing' : 'grab'
              }"
              @mousedown="startDragSnippet1"
              @touchstart="startDragSnippet1"
            >
              <div class="snippet-header">
                <span class="dot red"></span>
                <span class="dot yellow"></span>
                <span class="dot green"></span>
              </div>
              <pre><code>const developer = {
  name: "Nakry",
  skills: ["Vue", "js"]
}</code></pre>
            </div>
            <div
              class="code-snippet snippet-2"
              :class="{ 'dragging': isDraggingSnippet2, 'returning': !isDraggingSnippet2 }"
              :style="{
                transform: `translate(${snippet2Position.x}px, ${snippet2Position.y}px)`,
                cursor: isDraggingSnippet2 ? 'grabbing' : 'grab'
              }"
              @mousedown="startDragSnippet2"
              @touchstart="startDragSnippet2"
            >
              <div class="snippet-header">
                <span class="dot red"></span>
                <span class="dot yellow"></span>
                <span class="dot green"></span>
              </div>
              <pre><code>console.log("Hello!")</code></pre>
            </div>
            <div class="floating-badge badge-1">
              <i class="fab fa-vuejs"></i>
            </div>
            <div class="floating-badge badge-2">
              <i class="fab fa-react"></i>
            </div>
          </div>
        </div>
      </div>

      <div class="scroll-indicator" @click="scrollToSection('about')">
        <div class="mouse">
          <div class="wheel"></div>
        </div>
        <span>▼ PRESS START ▼</span>
      </div>
    </section>

    <!-- About — STAGE 2 -->
    <section class="about" id="about">
      <div class="container">
        <h2 class="section-title">
          <span class="title-number">STAGE 2</span> About Me
        </h2>
        <div class="about-content">
          <div class="about-text">
            <div class="dialogue-box">
              <div class="dialogue-name">NPC: TANG NAKRY</div>
              <p class="highlight-paragraph">
                Hello! I'm Tang Nakry, I am 22 years old. I am from Bantey Mean Chey province, and currently I study at Institute of Technology of Cambodia (ITC), department of Software Engineering. I have a passion for learning new technologies and continuously improving my skills.
              </p>
            </div>
            <p>
              I am a highly motivated and dedicated learner who actively seeks to expand my knowledge of modern technologies through hands-on projects and practical experience. I am passionate about strengthening my problem-solving skills, writing clean and efficient code, and developing reliable software solutions. I am strongly committed to continuous learning and professional growth, and I strive to contribute positively to development teams and technology-driven environments. My long-term goal is to build a successful career in software engineering by creating impactful applications and continuously improving my expertise in emerging technologies.
            </p>
            <p>
              <span class="title-number" style="font-size: 0.8rem;">⚔ INVENTORY:</span> Technologies I've been working with:
            </p>
            <ul class="tech-list">
              <li>Vue.js</li>
              <li>C, C++, C#</li>
              <li>Html, Css, Js</li>
              <li>MongoDB & MySQL</li>
              <li>Rust</li>
              <li>Java</li>
              <li>GDScript (Godot Engine)</li>
              <li>React (Ongoing)</li>
            </ul>
          </div>
          <div class="about-image">
            <div class="image-border">
              <img src="https://images.unsplash.com/photo-1498050108023-c5249f4df085?w=400&h=400&fit=crop" alt="Coding" />
              <div class="image-gradient"></div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Skills — STAGE 3 -->
    <SkillsSection
      :skills="skills"
      section-number="STAGE 3"
      title="Skills & Technologies"
      @reorder="handleSkillsReorder"
    />

    <!-- Projects — STAGE 4 -->
    <section class="projects" id="projects">
      <div class="container">
        <h2 class="section-title">
          <span class="title-number">STAGE 4</span> Featured Projects
        </h2>
        <div class="projects-grid">
          <ProjectCard
            v-for="(project, index) in projects"
            :key="index"
            :project="project"
            @view="handleProjectView"
            @code="handleProjectCode"
          />
        </div>
      </div>
    </section>

    <!-- Contact — STAGE 5 -->
    <section class="contact" id="contact">
      <div class="container">
        <h2 class="section-title center">
          <span class="title-number">STAGE 5</span> Get In Touch
        </h2>
        <div class="contact-content">
          <div class="dialogue-box">
            <div class="dialogue-name">QUEST GIVER</div>
            <p class="contact-text">
              I'm currently looking for new opportunities. Whether you have a question or
              just want to say hi, I'll try my best to get back to you!
            </p>
            <div class="dialogue-prompt">▼ CHOOSE YOUR METHOD ▼</div>
          </div>
          <div class="contact-methods">
            <a href="mailto:your@email.com" class="contact-card">
              <i class="fas fa-envelope"></i>
              <span>nakrey4399@email.com</span>
            </a>
            <a href="tel:+855123456789" class="contact-card">
              <i class="fa-solid fa-phone"></i>
              <span>+855 964748792</span>
            </a>
            <a href="https://linkedin.com" target="_blank" class="contact-card">
              <i class="fab fa-linkedin"></i>
              <span>LinkedIn</span>
            </a>
          </div>
          <a href="mailto:your@email.com" class="btn btn-primary btn-large">
            <span>▶ Say Hello</span>
            <i class="fas fa-paper-plane"></i>
            <div class="btn-shine"></div>
          </a>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="footer">
      <div class="container">
        <div class="footer-content">
          <div class="footer-logo">
            <span class="logo-bracket">&lt;</span>
            <span>TANG</span><span class="accent"> Nakry</span>
            <span class="logo-bracket">/&gt;</span>
          </div>
          <p>&copy; 2026 TANG Nakry. Built with <span class="heart"><img class="heart-icon" src="/heart.png" alt=""></span> and Vue.js</p>
          <div class="footer-links">
            <a href="https://github.com/NxaSenpai" target="_blank" aria-label="GitHub"><i class="fab fa-github"></i></a>
            <a href="https://linkedin.com" target="_blank" aria-label="LinkedIn"><i class="fab fa-linkedin"></i></a>
            <a href="https://twitter.com" target="_blank" aria-label="Twitter"><i class="fab fa-twitter"></i></a>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>
