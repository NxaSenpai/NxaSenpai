<script setup>
import { ref, inject, onMounted, onUnmounted, watch } from 'vue'
import SkillCard from './SkillCard.vue'

const props = defineProps({
  skills: {
    type: Array,
    required: true
  },
  sectionNumber: {
    type: String,
    default: '02.'
  },
  title: {
    type: String,
    default: 'Skills & Technologies'
  }
})

const emit = defineEmits(['update:skills', 'reorder'])

const isDarkMode = inject('isDarkMode', ref(true))

// Local copy of skills for drag reordering
const localSkills = ref([...props.skills])

// Watch for prop changes
watch(() => props.skills, (newSkills) => {
  localSkills.value = [...newSkills]
}, { deep: true })

// Drag state
const draggedIndex = ref(null)
const dragPosition = ref({ x: 0, y: 0 })
const dragStart = ref({ x: 0, y: 0 })
const isDragging = ref(false)
const dragOverIndex = ref(null)
const gridRef = ref(null)
const cardPositions = ref([])
const isAnimatingBack = ref(false)
const animatingIndex = ref(null)
const targetPosition = ref({ x: 0, y: 0 })

const cacheCardPositions = () => {
  if (!gridRef.value) return
  
  const wrappers = gridRef.value.querySelectorAll('.skill-drag-wrapper')
  cardPositions.value = Array.from(wrappers).map(wrapper => {
    const rect = wrapper.getBoundingClientRect()
    return {
      left: rect.left,
      top: rect.top,
      width: rect.width,
      height: rect.height
    }
  })
}

const getTargetIndex = (clientX, clientY) => {
  if (!gridRef.value) return null
  
  const wrappers = gridRef.value.querySelectorAll('.skill-drag-wrapper')
  for (let i = 0; i < wrappers.length; i++) {
    if (i === draggedIndex.value) continue
    
    const rect = wrappers[i].getBoundingClientRect()
    
    if (
      clientX >= rect.left &&
      clientX <= rect.right &&
      clientY >= rect.top &&
      clientY <= rect.bottom
    ) {
      return i
    }
  }
  return null
}

const getShiftStyle = (index) => {
  if (draggedIndex.value === null || dragOverIndex.value === null) {
    return {}
  }
  
  if (index === draggedIndex.value) {
    return {}
  }
  
  const dragIdx = draggedIndex.value
  const overIdx = dragOverIndex.value
  
  if (cardPositions.value.length === 0) return {}
  
  const currentPos = cardPositions.value[index]
  if (!currentPos) return {}
  
  if (dragIdx < overIdx) {
    if (index > dragIdx && index <= overIdx) {
      const prevIndex = index - 1
      const prevPos = cardPositions.value[prevIndex]
      if (!prevPos) return {}
      
      const shiftX = prevPos.left - currentPos.left
      const shiftY = prevPos.top - currentPos.top
      
      return { 
        transform: `translate(${shiftX}px, ${shiftY}px)`
      }
    }
  } else if (dragIdx > overIdx) {
    if (index >= overIdx && index < dragIdx) {
      const nextIndex = index + 1
      const nextPos = cardPositions.value[nextIndex]
      if (!nextPos) return {}
      
      const shiftX = nextPos.left - currentPos.left
      const shiftY = nextPos.top - currentPos.top
      
      return { 
        transform: `translate(${shiftX}px, ${shiftY}px)`
      }
    }
  }
  
  return {}
}

const startDrag = (e, index) => {
  e.preventDefault()
  
  cacheCardPositions()
  
  isDragging.value = true
  draggedIndex.value = index
  isAnimatingBack.value = false
  animatingIndex.value = null
  
  const clientX = e.type === 'touchstart' ? e.touches[0].clientX : e.clientX
  const clientY = e.type === 'touchstart' ? e.touches[0].clientY : e.clientY
  
  dragStart.value = { x: clientX, y: clientY }
  dragPosition.value = { x: 0, y: 0 }
}

const handleDrag = (e) => {
  if (!isDragging.value || draggedIndex.value === null) return
  
  const clientX = e.type === 'touchmove' ? e.touches[0].clientX : e.clientX
  const clientY = e.type === 'touchmove' ? e.touches[0].clientY : e.clientY
  
  dragPosition.value = {
    x: clientX - dragStart.value.x,
    y: clientY - dragStart.value.y
  }
  
  const targetIndex = getTargetIndex(clientX, clientY)
  if (targetIndex !== null && targetIndex !== draggedIndex.value) {
    dragOverIndex.value = targetIndex
  } else if (targetIndex === null) {
    const draggedCard = cardPositions.value[draggedIndex.value]
    if (draggedCard) {
      const currentX = draggedCard.left + dragPosition.value.x + draggedCard.width / 2
      const currentY = draggedCard.top + dragPosition.value.y + draggedCard.height / 2
      
      if (
        currentX >= draggedCard.left &&
        currentX <= draggedCard.left + draggedCard.width &&
        currentY >= draggedCard.top &&
        currentY <= draggedCard.top + draggedCard.height
      ) {
        dragOverIndex.value = null
      }
    }
  }
}

const stopDrag = () => {
  if (!isDragging.value) return
  
  const fromIndex = draggedIndex.value
  const toIndex = dragOverIndex.value
  
  if (toIndex !== null && fromIndex !== toIndex) {
    const targetPos = cardPositions.value[toIndex]
    const currentPos = cardPositions.value[fromIndex]
    
    if (targetPos && currentPos) {
      targetPosition.value = {
        x: targetPos.left - currentPos.left,
        y: targetPos.top - currentPos.top
      }
    }
  } else {
    targetPosition.value = { x: 0, y: 0 }
  }
  
  animatingIndex.value = fromIndex
  isAnimatingBack.value = true
  isDragging.value = false
  
  setTimeout(() => {
    if (fromIndex !== null && toIndex !== null && fromIndex !== toIndex) {
      const newSkills = [...localSkills.value]
      const [draggedItem] = newSkills.splice(fromIndex, 1)
      newSkills.splice(toIndex, 0, draggedItem)
      
      localSkills.value = newSkills
      emit('update:skills', newSkills)
      emit('reorder', { from: fromIndex, to: toIndex, skills: newSkills })
    }
    
    dragPosition.value = { x: 0, y: 0 }
    targetPosition.value = { x: 0, y: 0 }
    draggedIndex.value = null
    dragOverIndex.value = null
    cardPositions.value = []
    isAnimatingBack.value = false
    animatingIndex.value = null
  }, 250)
}

const getDraggedStyle = (index) => {
  if (isAnimatingBack.value && animatingIndex.value === index) {
    return {
      transform: `translate(${targetPosition.value.x}px, ${targetPosition.value.y}px)`,
      zIndex: 100,
      transition: 'transform 0.25s cubic-bezier(0.2, 0, 0, 1)'
    }
  }
  
  if (index !== draggedIndex.value) return {}
  
  return {
    transform: `translate(${dragPosition.value.x}px, ${dragPosition.value.y}px) scale(1.02)`,
    zIndex: 100
  }
}

const getWrapperClass = (index) => {
  return {
    'dragging': draggedIndex.value === index && !isAnimatingBack.value,
    'animating-back': isAnimatingBack.value && animatingIndex.value === index,
    'shifting': isDragging.value && draggedIndex.value !== index,
    'drag-over': dragOverIndex.value === index && draggedIndex.value !== index
  }
}

onMounted(() => {
  window.addEventListener('mousemove', handleDrag)
  window.addEventListener('mouseup', stopDrag)
  window.addEventListener('touchmove', handleDrag, { passive: false })
  window.addEventListener('touchend', stopDrag)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleDrag)
  window.removeEventListener('mouseup', stopDrag)
  window.removeEventListener('touchmove', handleDrag)
  window.removeEventListener('touchend', stopDrag)
})
</script>

<template>
  <section class="skills" id="skills" :class="{ 'dark-mode': isDarkMode, 'light-mode': !isDarkMode }">
    <div class="container">
      <h2 class="section-title">
        <span class="title-number">{{ sectionNumber }}</span> {{ title }}
      </h2>
      <div ref="gridRef" class="skills-grid" :class="{ 'is-dragging': isDragging }">
        <div 
          v-for="(skill, index) in localSkills" 
          :key="skill.name"
          class="skill-drag-wrapper"
          :class="getWrapperClass(index)"
          :style="draggedIndex === index || animatingIndex === index ? getDraggedStyle(index) : getShiftStyle(index)"
          :data-index="index"
          @mousedown="startDrag($event, index)"
          @touchstart="startDrag($event, index)"
        >
          <SkillCard 
            :skill="skill"
            :index="index"
          />
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.skills {
  padding: clamp(3rem, 8vw, 6rem) clamp(1rem, 4vw, 2rem);
  position: relative;
  overflow: hidden;
}

.container {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 clamp(1rem, 3vw, 2rem);
}

.section-title {
  font-size: clamp(1.5rem, 5vw, 3rem);
  font-weight: 800;
  margin-bottom: clamp(1.5rem, 4vw, 2rem);
  color: var(--text-primary);
  display: flex;
  align-items: center;
  gap: clamp(0.75rem, 2vw, 1.5rem);
  position: relative;
  flex-wrap: wrap;
}

.title-number {
  color: var(--accent);
  font-size: clamp(1rem, 3vw, 1.8rem);
  font-family: 'Fira Code', monospace;
  font-weight: 400;
}

.section-title::after {
  content: '';
  flex: 1;
  height: 2px;
  background: linear-gradient(90deg, var(--accent), transparent);
  max-width: 400px;
  min-width: 50px;
}

.skills-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 280px), 1fr));
  gap: clamp(1rem, 3vw, 2.5rem);
  position: relative;
}

.skills-grid.is-dragging {
  user-select: none;
}

.skill-drag-wrapper {
  position: relative;
  cursor: grab;
  border-radius: 16px;
  will-change: transform;
  touch-action: none;
}

.skill-drag-wrapper.shifting {
  transition: transform 0.25s cubic-bezier(0.2, 0, 0, 1);
}

.skill-drag-wrapper.dragging {
  transition: none;
  z-index: 100;
  cursor: grabbing;
}

.skill-drag-wrapper.animating-back {
  z-index: 100;
  cursor: grabbing;
}

.skill-drag-wrapper.dragging::after,
.skill-drag-wrapper.animating-back::after {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: 16px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
  pointer-events: none;
}

.skill-drag-wrapper.drag-over::before {
  content: '';
  position: absolute;
  inset: -3px;
  border-radius: 18px;
  background: var(--accent);
  opacity: 0.2;
  pointer-events: none;
  z-index: -1;
}

/* Dark/Light mode variables */
.dark-mode {
  --bg-primary: #0a192f;
  --bg-secondary: #112240;
  --text-primary: #ccd6f6;
  --text-secondary: #8892b0;
  --accent: #64ffda;
  --accent-glow: rgba(100, 255, 218, 0.3);
}

.light-mode {
  --bg-primary: #f8f9fa;
  --bg-secondary: #ffffff;
  --text-primary: #1a202c;
  --text-secondary: #4a5568;
  --accent: #0891b2;
  --accent-glow: rgba(8, 145, 178, 0.3);
}

/* Tablet */
@media (max-width: 1024px) {
  .skills-grid {
    grid-template-columns: repeat(auto-fill, minmax(min(100%, 240px), 1fr));
  }
}

/* Mobile */
@media (max-width: 768px) {
  .skills-grid {
    grid-template-columns: repeat(auto-fill, minmax(min(100%, 200px), 1fr));
    gap: 1rem;
  }
  
  .section-title::after {
    display: none;
  }
}

/* Small mobile */
@media (max-width: 480px) {
  .skills-grid {
    grid-template-columns: 1fr;
  }
}
</style>