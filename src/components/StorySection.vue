<script setup>
import { computed, ref, watch } from 'vue'
import TypewriterText from './TypewriterText.vue'
import ScrollIndicator from './ScrollIndicator.vue'
import ConfettiEffect from './ConfettiEffect.vue'
import MusicPlayer from './MusicPlayer.vue'

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
  musicUrl: {
    type: String,
    default: '',
  },
})

const hasBeenViewed = ref(false)
const confettiRef = ref(null)
const musicPlayerRef = ref(null)
const typewriterComplete = ref(false)

const isFirst = computed(() => props.colorIndex === 0)

const handleTypewriterComplete = async () => {
  typewriterComplete.value = true

  if (props.isLast && confettiRef.value) {
    confettiRef.value.triggerConfetti()
    // Start music when confetti triggers with a small delay
    if (musicPlayerRef.value) {
      setTimeout(async () => {
        try {
          // Try the muted autoplay trick first (works better on mobile)
          await musicPlayerRef.value.playMutedThenUnmute()
        } catch (error) {
          console.error('Failed to start music:', error)
        }
      }, 500)
    }
  }
}

const emit = defineEmits(['start-clicked'])

const handleStartClick = () => {
  // Emit event to parent to load next section
  emit('start-clicked', props.colorIndex + 1)
  // Scroll to next page
  window.scrollBy({
    top: window.innerHeight,
    behavior: 'smooth',
  })
}

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

const textColorClass = computed(() => {
  const colorNumber = (props.colorIndex % 3) + 1
  return colorNumber === 1 ? 'dark-text' : ''
})

// Show text if currently active OR has been viewed before
const shouldShowText = computed(() => props.isActive || hasBeenViewed.value)
</script>

<template>
  <section class="story-section" :class="colorClass">
    <ConfettiEffect v-if="isLast" ref="confettiRef" />
    <MusicPlayer v-if="isLast" ref="musicPlayerRef" :musicUrl="musicUrl" />
    <div class="content-wrapper">
      <div class="story-content" :class="textColorClass">
        <TypewriterText
          :text="story.text"
          :isVisible="shouldShowText"
          :speed="40"
          :sentenceDelay="600"
          :delay="300"
          @complete="handleTypewriterComplete"
        />
      </div>
    </div>

    <ScrollIndicator
      v-if="isActive && !isLast && !isFirst && typewriterComplete"
      :textColorClass="textColorClass"
    />

    <div v-if="isFirst && typewriterComplete && isActive" class="start-button-wrapper">
      <button class="start-button" @click="handleStartClick">Başla</button>
    </div>
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

.story-content.dark-text {
  color: var(--text-dark);
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

.start-button-wrapper {
  position: absolute;
  bottom: 4rem;
  left: 50%;
  transform: translateX(-50%);
  animation: fadeInUp 0.6s ease-out;
}

.start-button {
  padding: 0.875rem 2rem;
  font-size: 1rem;
  font-weight: 600;
  letter-spacing: 0.05em;
  color: #ffffff;
  background: linear-gradient(135deg, var(--color-2), var(--color-3));
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.start-button:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.start-button:active {
  transform: scale(0.95);
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
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
