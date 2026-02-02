<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { stories } from './data/stories.js'
import StorySection from './components/StorySection.vue'

const activeIndex = ref(0)
const sectionsRef = ref([])
let observer = null

const setSectionRef = (el, index) => {
  if (el) {
    // Get the actual DOM element from the component ref
    sectionsRef.value[index] = el.$el || el
  }
}

onMounted(() => {
  // Use IntersectionObserver for reliable detection with scroll-snap
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting && entry.intersectionRatio > 0.5) {
          const index = sectionsRef.value.indexOf(entry.target)
          if (index !== -1) {
            activeIndex.value = index
          }
        }
      })
    },
    {
      threshold: [0.5, 0.75, 1.0],
      rootMargin: '0px',
    },
  )

  // Observe all sections after a short delay to ensure refs are set
  setTimeout(() => {
    sectionsRef.value.forEach((section) => {
      if (section) {
        observer.observe(section)
      }
    })
  }, 100)
})

onUnmounted(() => {
  if (observer) {
    observer.disconnect()
  }
})
</script>

<template>
  <main class="story-container">
    <StorySection
      v-for="(story, index) in stories"
      :key="story.id"
      :ref="(el) => setSectionRef(el, index)"
      :story="story"
      :colorIndex="index"
      :isActive="activeIndex === index"
      :isLast="index === stories.length - 1"
    />
  </main>
</template>

<style>
.story-container {
  width: 100%;
}
</style>
