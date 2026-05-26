<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  skill: {
    type: Object,
    required: true
  },
  index: {
    type: Number,
    default: 0
  }
})

const isVisible = ref(false)

onMounted(() => {
  setTimeout(() => {
    isVisible.value = true
  }, props.index * 100)
})
</script>

<template>
  <div 
    class="skill-card"
    :class="{ 'visible': isVisible }"
    :style="{ 
      '--skill-color': skill.color, 
      '--animation-delay': index * 0.1 + 's',
      '--skill-level': skill.level + '%'
    }"
  >
    <div class="skill-icon">
      <i :class="skill.icon"></i>
      <div class="icon-bg"></div>
    </div>
    <h3>{{ skill.name }}</h3>
    <div class="skill-level">
      <div class="skill-bar">
        <div class="skill-progress" :class="{ 'animate': isVisible }"></div>
      </div>
      <span class="skill-percentage">{{ skill.level }}%</span>
    </div>
  </div>
</template>

<style scoped>
.skill-card {
  background: var(--bg-secondary);
  padding: clamp(1.5rem, 4vw, 2.5rem);
  border-radius: clamp(12px, 2vw, 16px);
  text-align: center;
  border: 2px solid transparent;
  transition: all 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
  position: relative;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  opacity: 0;
  transform: translateY(30px);
}

.skill-card.visible {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 0.6s ease, transform 0.6s ease;
  transition-delay: var(--animation-delay);
}

.skill-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--skill-color), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
}

@media (hover: hover) {
  .skill-card:hover {
    border-color: var(--skill-color);
    transform: translateY(-10px);
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4), 0 0 30px color-mix(in srgb, var(--skill-color) 30%, transparent);
  }

  .skill-card:hover::before {
    opacity: 0.15;
  }

  .skill-card:hover .icon-bg {
    transform: translate(-50%, -50%) scale(1.3);
    opacity: 0.2;
  }
}

.skill-icon {
  font-size: clamp(2.5rem, 6vw, 4rem);
  margin-bottom: clamp(1rem, 2vw, 1.5rem);
  color: var(--skill-color);
  position: relative;
  display: inline-block;
}

.icon-bg {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: clamp(50px, 10vw, 80px);
  height: clamp(50px, 10vw, 80px);
  background: var(--skill-color);
  border-radius: 50%;
  opacity: 0.1;
  transition: all 0.3s ease;
}

.skill-card h3 {
  font-size: clamp(1rem, 2.5vw, 1.4rem);
  margin-bottom: clamp(1rem, 2vw, 1.5rem);
  color: var(--text-primary);
  font-weight: 700;
}

.skill-level {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.skill-bar {
  width: 100%;
  height: clamp(4px, 1vw, 6px);
  background: var(--bg-tertiary);
  border-radius: 10px;
  overflow: hidden;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.2);
}

.skill-progress {
  height: 100%;
  background: linear-gradient(90deg, var(--skill-color), var(--accent));
  width: 0;
  border-radius: 10px;
  box-shadow: 0 0 10px var(--skill-color);
  transition: width 1.5s cubic-bezier(0.16, 1, 0.3, 1);
}

.skill-progress.animate {
  width: var(--skill-level);
}

.skill-percentage {
  font-size: clamp(0.75rem, 1.5vw, 0.9rem);
  color: var(--skill-color);
  font-weight: 700;
  font-family: 'Fira Code', monospace;
}

/* Touch devices - disable hover effects */
@media (hover: none) {
  .skill-card:active {
    transform: scale(0.98);
  }
}
</style>