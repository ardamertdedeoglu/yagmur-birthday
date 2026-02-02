<script setup>
import { ref, watch, onBeforeUnmount } from 'vue'

const props = defineProps({
  text: {
    type: String,
    required: true,
  },
  speed: {
    type: Number,
    default: 50,
  },
  sentenceDelay: {
    type: Number,
    default: 600,
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
const timeoutId = ref(null)

const typeText = () => {
  // Only start once
  if (hasStarted.value) return
  hasStarted.value = true

  displayedText.value = ''
  isTyping.value = true
  let index = 0

  const type = () => {
    if (index < props.text.length) {
      const currentChar = props.text.charAt(index)
      displayedText.value += currentChar
      index++

      // Check if current character is end of sentence
      const isSentenceEnd = currentChar === '.' || currentChar === '!' || currentChar === '?'
      const nextCharExists = index < props.text.length

      // If sentence ends and there's more text, use sentenceDelay; otherwise use normal speed
      const nextDelay = isSentenceEnd && nextCharExists ? props.sentenceDelay : props.speed
      timeoutId.value = setTimeout(type, nextDelay)
    } else {
      isTyping.value = false
      emit('complete')
    }
  }

  timeoutId.value = setTimeout(type, props.delay)
}

onBeforeUnmount(() => {
  if (timeoutId.value) {
    clearTimeout(timeoutId.value)
  }
})

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
