<script setup>
import { ref, onMounted, watch, provide, computed } from 'vue'
import ProjectCard from '../components/ProjectCard.vue'
import SkillsSection from '../components/SkillsSection.vue'
import '../assets/styles.css'

const isVisible = ref(false)
const isDarkMode = ref(true)

provide('isDarkMode', isDarkMode)

// Seasonal theme detection
const currentTheme = computed(() => {
  const now = new Date()
  const month = now.getMonth() // 0-11
  const day = now.getDate()
  
  // Halloween: October 15 - November 2
  if ((month === 9 && day >= 15) || (month === 10 && day <= 2)) {
    return 'halloween'
  }
  
  // Christmas: December 1 - December 31
  if (month === 11) {
    return 'christmas'
  }
  
  // New Year: January 1 - January 7
  if (month === 0 && day <= 7) {
    return 'newyear'
  }
  
  // Lunar New Year / Chinese New Year: Usually late Jan - mid Feb
  // Approximating as Jan 20 - Feb 20
  if ((month === 0 && day >= 20) || (month === 1 && day <= 20)) {
    return 'lunar'
  }
  
  // Valentine's Day: February 7 - February 14
  if (month === 1 && day >= 7 && day <= 14) {
    return 'valentine'
  }
  
  // Spring: March - May
  if (month >= 2 && month <= 4) {
    return 'spring'
  }
  
  // Summer: June - August
  if (month >= 5 && month <= 7) {
    return 'summer'
  }
  
  // Autumn/Fall: September - November (before Halloween)
  if (month >= 8 && month <= 10) {
    return 'autumn'
  }
  
  return 'default'
})

// Theme-specific particles
const themeParticles = computed(() => {
  switch (currentTheme.value) {
    case 'halloween':
      return ['🎃', '👻', '🦇', '🕷️', '💀', '🕸️', '🌙', '⭐']
    case 'christmas':
      return ['❄️', '🎄', '🎅', '⭐', '🎁', '🔔', '❄️', '✨']
    case 'newyear':
      return ['🎆', '🎇', '✨', '🎊', '🎉', '⭐', '🌟', '💫']
    case 'lunar':
      return ['🧧', '🏮', '🐉', '🎆', '🧨', '💰', '🌸', '✨']
    case 'valentine':
      return ['❤️', '💕', '💖', '💗', '💘', '🌹', '💝', '✨']
    case 'spring':
      return ['🌸', '🌺', '🌷', '🦋', '🐝', '🌼', '☘️', '🌿']
    case 'summer':
      return ['☀️', '🌊', '🏖️', '🌴', '🍉', '🌺', '⭐', '✨']
    case 'autumn':
      return ['🍂', '🍁', '🌾', '🎃', '🌰', '🍄', '🦉', '🌙']
    default:
      return ['✨', '⭐', '💫', '🌟', '✦', '✧', '⚡', '💎']
  }
})

// Generate random particles
const particles = computed(() => {
  const items = []
  for (let i = 0; i < 25; i++) {
    items.push({
      id: i,
      emoji: themeParticles.value[i % themeParticles.value.length],
      left: Math.random() * 100,
      top: Math.random() * 100,
      delay: Math.random() * 10,
      duration: 10 + Math.random() * 15,
      size: 0.8 + Math.random() * 1.2
    })
  }
  return items
})

// Draggable snippet state
const snippet1Position = ref({ x: 0, y: 0 })
const snippet2Position = ref({ x: 0, y: 0 })
const isDraggingSnippet1 = ref(false)
const isDraggingSnippet2 = ref(false)
const dragStart = ref({ x: 0, y: 0 })

// Dragging handlers for snippet 1
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

// Dragging handlers for snippet 2
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
})

watch(isDarkMode, () => {
  applyTheme()
})

const applyTheme = () => {
  document.documentElement.setAttribute('data-theme', isDarkMode.value ? 'dark' : 'light')
  document.body.style.backgroundColor = isDarkMode.value ? '#0a192f' : '#f8f9fa'
  document.body.style.color = isDarkMode.value ? '#ccd6f6' : '#1a202c'
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
  console.log(`Skill moved from position ${from} to ${to}`)
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
  <div class="home" :class="[
    isDarkMode ? 'dark-mode' : 'light-mode',
    `theme-${currentTheme}`
  ]">
    <!-- Animated Background -->
    <div class="animated-bg">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
      
      <!-- Seasonal Particles -->
      <div class="seasonal-particles">
        <div 
          v-for="particle in particles" 
          :key="particle.id" 
          class="seasonal-particle"
          :style="{
            left: `${particle.left}%`,
            top: `${particle.top}%`,
            animationDelay: `${particle.delay}s`,
            animationDuration: `${particle.duration}s`,
            fontSize: `${particle.size}rem`
          }"
        >
          {{ particle.emoji }}
        </div>
      </div>
    </div>


    <button class="theme-toggle" @click="toggleTheme" aria-label="Toggle theme">
      <i :class="isDarkMode ? 'fas fa-sun' : 'fas fa-moon'"></i>
    </button>

    <!-- Navigation -->
    <nav class="navbar" :class="{ 'scrolled': false }">
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
          <li><a href="#home" @click="closeMobileMenu"><span class="nav-number">00.</span>Home</a></li>
          <li><a href="#about" @click="closeMobileMenu"><span class="nav-number">01.</span>About</a></li>
          <li><a href="#skills" @click="closeMobileMenu"><span class="nav-number">02.</span>Skills</a></li>
          <li><a href="#projects" @click="closeMobileMenu"><span class="nav-number">03.</span>Projects</a></li>
          <li><a href="#contact" @click="closeMobileMenu"><span class="nav-number">04.</span>Contact</a></li>
          <li>
            <a href="/resume.pdf" target="_blank" class="resume-btn">
            <i class="fa-solid fa-circle-down"></i> Resume
            </a>
          </li>
        </ul>
      </div>
    </nav>

    <!-- Hero Section -->
    <section class="hero" id="home" :class="{ 'fade-in': isVisible }">
      <div class="hero-content">
        <div class="text-content">
          <div class="greeting">
            <span class="wave">👋</span> Hello, I'm
          </div>
          <h1 class="title">
            TANG <span class="gradient-text">Nakry</span>
          </h1>
          <h2 class="subtitle">
            <span class="typing-container">
              <span class="typing-text">Web & App Developer</span>
            </span>
          </h2>
          <p class="description">
            Hello! I'm Nakry, just a guy who love coding live in Cambodia. 
              I enjoy creating things that live on the internet, whether that be websites, 
              applications, or anything in between also games.
          </p>
          
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
              <div class="stat-number">0</div>
              <div class="stat-label">Current Projects</div>
            </div>
          </div>

          <div class="cta-buttons">
            <button class="btn btn-primary" @click="scrollToSection('projects')">
              <span>View Projects</span>
              <div class="btn-shine"></div>
            </button>
            <button class="btn btn-secondary" @click="scrollToSection('contact')">
              <span>Contact Me</span>
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
            <a href="https://youtube.com" target="_blank" class="social-icon" aria-label="Youtube">
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
        <span>Scroll Down</span>
      </div>
    </section>

    <!-- About Section -->
    <section class="about" id="about">
      <div class="container">
        <h2 class="section-title">
          <span class="title-number">01.</span> About Me
        </h2>
        <div class="about-content">
          <div class="about-text">
            <p class="highlight-paragraph">
              Hello! I'm Tang Nakry, I am 22 years old.I am from Bantey Mean Chey province, and currently I study at Institute of Technology of Cambodia (ITC) department Software Engineering. I have a passion for learning new technologies and continuously improving my skills.
            </p>
            <p>
              I am a highly motivated and dedicated learner who actively seeks to expand my knowledge of modern technologies through hands-on projects and practical experience. I am passionate about strengthening my problem-solving skills, writing clean and efficient code, and developing reliable software solutions. I am strongly committed to continuous learning and professional growth, and I strive to contribute positively to development teams and technology-driven environments. My long-term goal is to build a successful career in software engineering by creating impactful applications and continuously improving my expertise in emerging technologies.
            </p>
            <p>
              Here are a few technologies I've been working with recently:
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

    <!-- Skills Section -->
    <SkillsSection 
      :skills="skills"
      section-number="02."
      title="Skills & Technologies"
      @reorder="handleSkillsReorder"
    />

    <!-- Projects Section -->
    <section class="projects" id="projects">
      <div class="container">
        <h2 class="section-title">
          <span class="title-number">03.</span> Featured Projects
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

    <!-- Contact Section -->
    <section class="contact" id="contact">
      <div class="container">
        <h2 class="section-title center">
          <span class="title-number">04.</span> Get In Touch
        </h2>
        <div class="contact-content">
          <p class="contact-text">
            I'm currently looking for new opportunities. Whether you have a question or 
            just want to say hi, I'll try my best to get back to you!
          </p>
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
            <span>Say Hello</span>
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