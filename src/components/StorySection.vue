<script setup>
import { computed, ref, watch } from 'vue'
import TypewriterText from './TypewriterText.vue'
import ScrollIndicator from './ScrollIndicator.vue'

const props = defineProps({
  story: {
    type: Object,
    required: true,
  },
  colorIndex: {
    type: Number,
    required: true,
  },
  isActive: {
    type: Boolean,
    default: false,
  },
  isLast: {
    type: Boolean,
    default: false,
  },
})

const hasBeenViewed = ref(false)

// Once a section becomes active, mark it as viewed
watch(
  () => props.isActive,
  (newVal) => {
    if (newVal) {
      hasBeenViewed.value = true
    }
  },
)

const colorClass = computed(() => {
  const colorNumber = (props.colorIndex % 3) + 1
  return `color-${colorNumber}`
})

// Show text if currently active OR has been viewed before
const shouldShowText = computed(() => props.isActive || hasBeenViewed.value)
</script>

<template>
  <section class="story-section" :class="colorClass">
    <div class="content-wrapper">
      <div class="story-content">
        <TypewriterText :text="story.text" :isVisible="shouldShowText" :speed="40" :delay="300" />
      </div>
    </div>

    <ScrollIndicator :show="isActive && !isLast" />
  </section>
</template>

<style scoped>
.story-section {
  position: relative;
  height: 100vh;
  height: 100dvh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: var(--spacing-md);
  scroll-snap-align: start;
  scroll-snap-stop: always;
  transition: background-color var(--transition-slow);
  overflow: hidden;
}

.story-section.color-1 {
  background-color: var(--color-1);
}

.story-section.color-2 {
  background-color: var(--color-2);
}

.story-section.color-3 {
  background-color: var(--color-3);
}

.content-wrapper {
  width: 100%;
  max-width: 800px;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 200px;
}

.story-content {
  text-align: center;
}

/* Slide up transition */
.slide-up-enter-active {
  transition: all 0.6s ease-out;
}

.slide-up-leave-active {
  transition: all 0.3s ease-in;
}

.slide-up-enter-from {
  opacity: 0;
  transform: translateY(30px);
}

.slide-up-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}

/* Responsive adjustments */
@media (max-width: 480px) {
  .story-section {
    padding: var(--spacing-sm);
  }
}
</style>
