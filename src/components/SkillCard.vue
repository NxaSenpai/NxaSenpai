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
  text-align: center;
  border: 2px solid var(--accent);
  transition: all 0.15s steps(2);
  position: relative;
  overflow: hidden;
  box-shadow:
    4px 4px 0 0 rgba(0,0,0,0.5),
    0 0 12px var(--accent-glow);
  opacity: 0;
  transform: translateY(20px);
}

.skill-card::after {
  content: '';
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0,0,0,0.03) 2px,
    rgba(0,0,0,0.03) 4px
  );
  pointer-events: none;
  z-index: 1;
}

.skill-card.visible {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 0.4s steps(4), transform 0.4s steps(4);
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
  transition: opacity 0.2s steps(2);
  z-index: 0;
}

@media (hover: hover) {
  .skill-card:hover {
    border-color: var(--skill-color);
    transform: translateY(-6px) translate(-2px, -2px);
    box-shadow:
      6px 6px 0 0 rgba(0,0,0,0.5),
      0 0 25px color-mix(in srgb, var(--skill-color) 40%, transparent);
  }

  .skill-card:hover::before {
    opacity: 0.12;
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
  z-index: 2;
  filter: drop-shadow(0 0 10px color-mix(in srgb, var(--skill-color) 50%, transparent));
}

.icon-bg {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: clamp(50px, 10vw, 80px);
  height: clamp(50px, 10vw, 80px);
  background: var(--skill-color);
  opacity: 0.08;
  transition: all 0.2s steps(2);
  z-index: -1;
}

.skill-card h3 {
  font-size: clamp(0.65rem, 1.8vw, 0.9rem);
  margin-bottom: clamp(1rem, 2vw, 1.5rem);
  color: var(--text-primary);
  font-weight: 700;
  font-family: var(--font-heading);
  position: relative;
  z-index: 2;
}

.skill-level {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  position: relative;
  z-index: 2;
}

.skill-bar {
  width: 100%;
  height: clamp(8px, 1.5vw, 12px);
  background: var(--bg-tertiary);
  overflow: hidden;
  border: 2px solid rgba(255,255,255,0.1);
  position: relative;
  box-shadow: inset 0 2px 0 rgba(0,0,0,0.4);
}

.skill-progress {
  height: 100%;
  background: repeating-linear-gradient(
    90deg,
    var(--skill-color) 0px,
    var(--skill-color) 6px,
    color-mix(in srgb, var(--skill-color) 60%, #000) 6px,
    color-mix(in srgb, var(--skill-color) 60%, #000) 8px
  );
  width: 0;
  box-shadow: 0 0 10px var(--skill-color);
  transition: width 1.2s steps(12);
  position: relative;
}

.skill-progress::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 50%;
  background: rgba(255,255,255,0.2);
}

.skill-progress.animate {
  width: var(--skill-level);
}

.skill-percentage {
  font-size: clamp(0.6rem, 1.2vw, 0.75rem);
  color: var(--skill-color);
  font-weight: 700;
  font-family: var(--font-heading);
  text-shadow: 0 0 8px color-mix(in srgb, var(--skill-color) 50%, transparent);
}

@media (hover: none) {
  .skill-card:active {
    transform: scale(0.97);
  }
}
</style>
