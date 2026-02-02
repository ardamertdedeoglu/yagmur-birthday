<script setup>
defineProps({
  show: {
    type: Boolean,
    default: true,
  },
})
</script>

<template>
  <Transition name="fade">
    <div v-if="show" class="scroll-indicator">
      <span class="scroll-text">Scroll</span>
      <div class="chevrons">
        <span class="chevron"></span>
        <span class="chevron"></span>
        <span class="chevron"></span>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.scroll-indicator {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.scroll-text {
  font-size: 0.875rem;
  text-transform: uppercase;
  letter-spacing: 0.15em;
  opacity: 0.8;
}

.chevrons {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0;
}

.chevron {
  width: 20px;
  height: 20px;
  border-right: 2px solid currentColor;
  border-bottom: 2px solid currentColor;
  transform: rotate(45deg);
  opacity: 0;
  animation: chevron-pulse 2s infinite;
}

.chevron:nth-child(1) {
  animation-delay: 0s;
}

.chevron:nth-child(2) {
  animation-delay: 0.15s;
  margin-top: -10px;
}

.chevron:nth-child(3) {
  animation-delay: 0.3s;
  margin-top: -10px;
}

@keyframes chevron-pulse {
  0%,
  100% {
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
