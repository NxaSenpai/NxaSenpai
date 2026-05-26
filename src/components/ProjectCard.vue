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
  border-radius: clamp(12px, 2vw, 16px);
  overflow: hidden;
  border: 2px solid transparent;
  transition: all 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

@media (hover: hover) {
  .project-card:hover {
    border-color: var(--accent);
    transform: translateY(-10px);
    box-shadow: 0 0 30px var(--accent-glow);
  }

  .project-card:hover .project-image img {
    transform: scale(1.15);
  }

  .project-card:hover .project-overlay {
    opacity: 1;
  }
}

.project-image {
  position: relative;
  overflow: hidden;
  height: clamp(180px, 25vw, 220px);
}

.project-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.6s cubic-bezier(0.16, 1, 0.3, 1);
}

.project-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, rgba(10, 25, 47, 0.95), rgba(29, 53, 87, 0.95));
  display: flex;
  align-items: center;
  justify-content: center;
  gap: clamp(0.8rem, 2vw, 1.2rem);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.overlay-btn {
  padding: clamp(0.75rem, 2vw, 1rem) clamp(1rem, 3vw, 1.8rem);
  background: var(--accent);
  color: var(--bg-primary);
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-weight: 700;
  font-size: clamp(0.8rem, 1.5vw, 1rem);
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

.overlay-btn:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 0 30px var(--accent-glow);
}

.project-content {
  padding: clamp(1.25rem, 3vw, 2rem);
}

.project-content h3 {
  font-size: clamp(1.1rem, 2.5vw, 1.5rem);
  margin-bottom: clamp(0.5rem, 1.5vw, 0.8rem);
  color: var(--text-primary);
  font-weight: 700;
}

.project-content p {
  color: var(--text-secondary);
  margin-bottom: clamp(1rem, 2vw, 1.5rem);
  line-height: 1.8;
  font-size: clamp(0.85rem, 1.5vw, 1rem);
}

.project-tags {
  display: flex;
  gap: clamp(0.5rem, 1vw, 0.8rem);
  flex-wrap: wrap;
}

.tag {
  padding: clamp(0.35rem, 1vw, 0.5rem) clamp(0.6rem, 1.5vw, 1rem);
  background: var(--accent-glow);
  color: var(--accent);
  border-radius: 25px;
  font-size: clamp(0.7rem, 1.3vw, 0.9rem);
  border: 1px solid var(--accent);
  font-weight: 600;
  transition: all 0.3s ease;
}

@media (hover: hover) {
  .tag:hover {
    background: var(--accent);
    color: var(--bg-primary);
    transform: translateY(-2px);
  }
}

/* Touch devices */
@media (hover: none) {
  .project-overlay {
    opacity: 1;
    background: linear-gradient(to top, rgba(10, 25, 47, 0.95) 0%, transparent 100%);
    align-items: flex-end;
    padding-bottom: 1rem;
  }
}

/* Mobile adjustments */
@media (max-width: 480px) {
  .overlay-btn span {
    display: none;
  }
  
  .overlay-btn {
    padding: 1rem;
    border-radius: 50%;
  }
}
</style>