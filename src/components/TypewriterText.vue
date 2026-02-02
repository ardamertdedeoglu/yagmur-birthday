<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  text: {
    type: String,
    required: true,
  },
  speed: {
    type: Number,
    default: 50,
  },
  delay: {
    type: Number,
    default: 0,
  },
  isVisible: {
    type: Boolean,
    default: true,
  },
})

const emit = defineEmits(['complete'])

const displayedText = ref('')
const isTyping = ref(false)
const hasStarted = ref(false)
let timeoutId = null

const typeText = () => {
  // Only start once
  if (hasStarted.value) return
  hasStarted.value = true

  displayedText.value = ''
  isTyping.value = true
  let index = 0

  const type = () => {
    if (index < props.text.length) {
      displayedText.value += props.text.charAt(index)
      index++
      timeoutId = setTimeout(type, props.speed)
    } else {
      isTyping.value = false
      emit('complete')
    }
  }

  timeoutId = setTimeout(type, props.delay)
}

// Watch for visibility and start typing when becomes visible
watch(
  () => props.isVisible,
  (newVal) => {
    if (newVal && !hasStarted.value) {
      typeText()
    }
  },
  { immediate: true },
)
</script>

<template>
  <p class="typewriter-text">
    <span>{{ displayedText }}</span>
    <span v-if="isTyping" class="cursor">|</span>
  </p>
</template>

<style scoped>
.typewriter-text {
  font-size: var(--font-size-lg);
  line-height: var(--line-height);
  max-width: 600px;
  text-align: center;
}

.cursor {
  display: inline-block;
  animation: blink 0.7s infinite;
  margin-left: 2px;
}

@keyframes blink {
  0%,
  50% {
    opacity: 1;
  }
  51%,
  100% {
    opacity: 0;
  }
}
</style>
