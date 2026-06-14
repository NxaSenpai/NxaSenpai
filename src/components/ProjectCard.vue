<script setup>
defineProps({
  project: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['view', 'code'])
</script>

<template>
  <div class="project-card">
    <div class="project-image">
      <img :src="project.image" :alt="project.title" loading="lazy" />
      <!-- Scanline overlay -->
      <div class="scanlines"></div>
      <div class="project-overlay">
        <button class="overlay-btn" @click="emit('view', project)">
          <i class="fas fa-eye"></i> <span>View</span>
        </button>
        <button class="overlay-btn" @click="emit('code', project)">
          <i class="fab fa-github"></i> <span>Code</span>
        </button>
      </div>
    </div>
    <div class="project-content">
      <h3>{{ project.title }}</h3>
      <p>{{ project.description }}</p>
      <div class="project-tags">
        <span v-for="tag in project.tags" :key="tag" class="tag">{{ tag }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.project-card {
  background: var(--bg-primary);
  overflow: hidden;
  border: 2px solid var(--accent);
  transition: all 0.15s steps(2);
  box-shadow:
    4px 4px 0 0 rgba(0,0,0,0.5),
    0 0 12px var(--accent-glow);
  position: relative;
}

.project-card::after {
  content: '';
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0,0,0,0.04) 2px,
    rgba(0,0,0,0.04) 4px
  );
  pointer-events: none;
  z-index: 5;
}

@media (hover: hover) {
  .project-card:hover {
    border-color: var(--cyan);
    transform: translateY(-6px) translate(-2px, -2px);
    box-shadow:
      6px 6px 0 0 rgba(0,0,0,0.5),
      0 0 25px var(--cyan-glow);
  }

  .project-card:hover .project-image img {
    transform: scale(1.1);
  }

  .project-card:hover .project-overlay {
    opacity: 1;
  }
}

.project-image {
  position: relative;
  overflow: hidden;
  height: clamp(180px, 25vw, 220px);
  border-bottom: 2px solid var(--accent);
}

.project-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.4s steps(6);
  image-rendaling: pixelated;
}

.scanlines {
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0,0,0,0.08) 2px,
    rgba(0,0,0,0.08) 4px
  );
  pointer-events: none;
  z-index: 1;
}

.project-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(10, 10, 15, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: clamp(0.8rem, 2vw, 1.2rem);
  opacity: 0;
  transition: opacity 0.2s steps(3);
  z-index: 2;
}

.overlay-btn {
  padding: clamp(0.7rem, 1.5vw, 0.9rem) clamp(1rem, 2.5vw, 1.5rem);
  background: var(--accent);
  color: var(--bg-primary);
  border: 2px solid var(--accent);
  cursor: pointer;
  font-weight: 700;
  font-size: clamp(0.6rem, 1vw, 0.75rem);
  transition: all 0.15s steps(2);
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-family: var(--font-heading);
  box-shadow:
    3px 3px 0 rgba(0,0,0,0.5),
    0 0 10px var(--accent-glow);
}

.overlay-btn:hover {
  transform: translate(-2px, -2px);
  box-shadow:
    5px 5px 0 rgba(0,0,0,0.5),
    0 0 20px var(--accent-glow);
}

.project-content {
  padding: clamp(1.25rem, 3vw, 2rem);
  position: relative;
  z-index: 1;
}

.project-content h3 {
  font-size: clamp(0.7rem, 1.8vw, 0.95rem);
  margin-bottom: clamp(0.5rem, 1.5vw, 0.8rem);
  color: var(--text-primary);
  font-weight: 700;
  font-family: var(--font-heading);
}

.project-content p {
  color: var(--text-secondary);
  margin-bottom: clamp(1rem, 2vw, 1.5rem);
  line-height: 1.8;
  font-size: clamp(0.8rem, 1.3vw, 0.9rem);
}

.project-tags {
  display: flex;
  gap: clamp(0.5rem, 1vw, 0.8rem);
  flex-wrap: wrap;
}

.tag {
  padding: clamp(0.3rem, 0.8vw, 0.4rem) clamp(0.5rem, 1.2vw, 0.8rem);
  background: var(--bg-tertiary);
  color: var(--accent);
  font-size: clamp(0.55rem, 1vw, 0.7rem);
  border: 1px solid var(--accent);
  font-weight: 600;
  transition: all 0.15s steps(2);
  font-family: var(--font-heading);
  box-shadow: 2px 2px 0 rgba(0,0,0,0.4);
}

@media (hover: hover) {
  .tag:hover {
    background: var(--accent);
    color: var(--bg-primary);
    transform: translateY(-2px);
    box-shadow:
      3px 3px 0 rgba(0,0,0,0.4),
      0 0 8px var(--accent-glow);
  }
}

@media (hover: none) {
  .project-overlay {
    opacity: 1;
    background: linear-gradient(to top, rgba(10, 10, 15, 0.95) 0%, transparent 100%);
    align-items: flex-end;
    padding-bottom: 1rem;
  }
}

@media (max-width: 480px) {
  .overlay-btn span {
    display: none;
  }

  .overlay-btn {
    padding: 0.8rem;
  }
}
</style>
