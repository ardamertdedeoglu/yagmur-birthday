<script setup>
import { onMounted } from 'vue'

defineProps({
  trigger: {
    type: Boolean,
    default: false,
  },
})

const confetti = () => {
  // Create confetti pieces
  const count = 50
  const defaults = {
    origin: { y: 0.7 },
  }

  function fire(particleRatio, opts) {
    if (typeof window.confetti === 'undefined') {
      // Fallback if confetti library isn't loaded
      createSimpleConfetti()
      return
    }

    window.confetti(
      Object.assign({}, defaults, opts, {
        particleCount: Math.floor(count * particleRatio),
      }),
    )
  }

  fire(0.25, {
    spread: 26,
    startVelocity: 55,
  })
  fire(0.2, {
    spread: 60,
  })
  fire(0.35, {
    spread: 100,
    decay: 0.91,
    scalar: 0.8,
  })
  fire(0.1, {
    spread: 120,
    startVelocity: 25,
    decay: 0.92,
    scalar: 1.2,
  })
  fire(0.1, {
    spread: 120,
    startVelocity: 45,
  })
}

const createSimpleConfetti = () => {
  // Simple CSS-based confetti fallback
  const confettiPieces = document.querySelectorAll('.confetti-piece')
  confettiPieces.forEach((piece) => {
    piece.classList.remove('animate')
    void piece.offsetWidth // Trigger reflow to restart animation
    piece.classList.add('animate')
  })
}

onMounted(() => {
  // Load confetti library if not already loaded
  if (typeof window.confetti === 'undefined') {
    const script = document.createElement('script')
    script.src = 'https://cdn.jsdelivr.net/npm/canvas-confetti@1.9.0/dist/confetti.browser.min.js'
    document.head.appendChild(script)
  }
})

const handleConfetti = () => {
  if (typeof window.confetti !== 'undefined') {
    confetti()
  } else {
    createSimpleConfetti()
  }
}

defineExpose({
  triggerConfetti: handleConfetti,
})
</script>

<template>
  <div class="confetti-container">
    <!-- CSS-based confetti pieces for fallback -->
    <div
      v-for="i in 20"
      :key="i"
      class="confetti-piece"
      :style="{ left: Math.random() * 100 + '%' }"
    ></div>
  </div>
</template>

<style scoped>
.confetti-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 9999;
}

.confetti-piece {
  position: absolute;
  width: 10px;
  height: 10px;
  background: radial-gradient(
    circle,
    #ff6b6b,
    #ffa500,
    #ffd93d,
    #6bcf7f,
    #4ecdc4,
    #45b7d1,
    #f38181
  );
  opacity: 0;
}

.confetti-piece.animate {
  animation: confetti-fall 3s ease-in forwards;
}

@keyframes confetti-fall {
  0% {
    opacity: 1;
    transform: translateY(-10vh) rotateZ(0deg);
  }
  100% {
    opacity: 0;
    transform: translateY(100vh) rotateZ(720deg);
  }
}
</style>
