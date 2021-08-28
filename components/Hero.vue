<template>
  <div class="hero animate__animated animate__zoomIn">
    <img src="../assets/hero.jpg" alt="wallpaper" />
    <div class="text-container">
      <div class="text">
        <h1><span>Now </span>Streaming</h1>
        <a href="#movies" class="button">View Movies</a>
        <button class="button" @click="reload">Home</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'

export default defineComponent({
  name: 'Hero',
  setup() {
    const reload = () => {
      window.location.reload()
    }

    return {
      reload,
    }
  },
})
</script>
