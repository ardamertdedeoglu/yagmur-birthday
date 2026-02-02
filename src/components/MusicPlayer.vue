<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { Howl } from 'howler'

const props = defineProps({
  musicUrl: {
    type: String,
    default: '',
  },
})

const isPlaying = ref(false)
let sound = null

onMounted(() => {
  if (props.musicUrl) {
    sound = new Howl({
      src: [props.musicUrl],
      html5: true,
      loop: true,
      preload: true,
      onload: () => {
        console.log('Audio loaded successfully:', props.musicUrl)
      },
      onloaderror: (id, error) => {
        console.error('Audio load error:', error, 'URL:', props.musicUrl)
      },
      onplay: () => {
        isPlaying.value = true
        console.log('Music playing')
      },
      onstop: () => {
        isPlaying.value = false
      },
    })
  }
})

onBeforeUnmount(() => {
  if (sound) {
    sound.stop()
    sound.unload()
  }
})

const toggleMusic = async () => {
  if (!sound) {
    console.error('Sound not initialized')
    return
  }

  if (isPlaying.value) {
    sound.stop()
    isPlaying.value = false
  } else {
    try {
      console.log('Attempting to play music with Howler...')
      sound.play()
    } catch (error) {
      console.error('Play failed:', error)
      isPlaying.value = false
    }
  }
}

const playMutedThenUnmute = async () => {
  if (!sound) {
    console.error('Sound not initialized')
    return
  }
  try {
    // Set volume to 0, play, then restore volume
    const originalVolume = sound.volume()
    sound.volume(0)
    sound.play()
    console.log('Playing muted with Howler...')

    // Unmute after a short delay
    setTimeout(() => {
      sound.volume(originalVolume)
      console.log('Music unmuted and playing')
    }, 100)
  } catch (error) {
    console.error('Muted play failed:', error)
  }
}

const handleMusicUrl = (url) => {
  if (sound) {
    sound.unload()
  }
  sound = new Howl({
    src: [url],
    html5: true,
    loop: true,
    preload: true,
  })
}

defineExpose({
  setMusicUrl: handleMusicUrl,
  toggleMusic,
  playMutedThenUnmute,
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
