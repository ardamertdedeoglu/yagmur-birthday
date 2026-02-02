<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  musicUrl: {
    type: String,
    default: '',
  },
})

const isPlaying = ref(false)
const audioRef = ref(null)

onMounted(() => {
  if (audioRef.value) {
    audioRef.value.addEventListener('error', (e) => {
      console.error('Audio load error:', e.target.error.message, 'URL:', props.musicUrl)
    })
    audioRef.value.addEventListener('canplay', () => {
      console.log('Audio ready to play:', props.musicUrl)
    })
  }
})

const toggleMusic = () => {
  if (!audioRef.value) {
    console.error('Audio element not found')
    return
  }

  if (isPlaying.value) {
    audioRef.value.pause()
  } else {
    const playPromise = audioRef.value.play()
    if (playPromise !== undefined) {
      playPromise.catch((error) => {
        console.error('Autoplay was prevented:', error)
      })
    }
  }
  isPlaying.value = !isPlaying.value
}

const handleMusicUrl = (url) => {
  if (audioRef.value) {
    audioRef.value.src = url
  }
}

defineExpose({
  setMusicUrl: handleMusicUrl,
  toggleMusic,
})
</script>

<template>
  <div class="music-player">
    <button
      class="music-button"
      @click="toggleMusic"
      :title="isPlaying ? 'Pause music' : 'Play music'"
    >
      <svg v-if="isPlaying" width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
        <!-- Pause icon -->
        <rect x="6" y="4" width="4" height="16" />
        <rect x="14" y="4" width="4" height="16" />
      </svg>
      <svg v-else width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
        <!-- Play icon -->
        <path d="M8 5v14l11-7z" />
      </svg>
    </button>
    <audio ref="audioRef" :src="musicUrl" loop crossorigin="anonymous"></audio>
  </div>
</template>

<style scoped>
.music-player {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  z-index: 1000;
}

.music-button {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.2);
  border: 2px solid rgba(255, 255, 255, 0.4);
  color: rgba(255, 255, 255, 0.8);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
}

.music-button:hover {
  background: rgba(255, 255, 255, 0.3);
  border-color: rgba(255, 255, 255, 0.6);
  color: rgba(255, 255, 255, 1);
  transform: scale(1.1);
}

.music-button:active {
  transform: scale(0.95);
}

@media (max-width: 480px) {
  .music-player {
    bottom: 1rem;
    right: 1rem;
  }

  .music-button {
    width: 45px;
    height: 45px;
  }
}
</style>
