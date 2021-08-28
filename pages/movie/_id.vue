<template>
  <!-- Loading -->
  <Loading v-if="!loading" />

  <!-- Movie Info -->
  <div v-else class="single-movie container animate__animated animate__zoomInDown">
    <NuxtLink class="button" :to="{ name: 'index' }"> Back </NuxtLink>
    <div class="movie-info">
      <div class="movie-img">
        <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="" />
      </div>
      <div class="movie-content">
        <h1>Title:{{ movie.title }}</h1>
        <p class="movie-fact tagline"><span>Tagline:</span> "{{ movie.tagline }}"</p>
        <p class="movie-fact">
          <span>Released:</span>
          {{
            new Date(movie.release_date).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
            })
          }}
        </p>
        <p class="movie-fact"><span>Duration:</span> {{ movie.runtime }} minutes</p>
        <p class="movie-fact"><span>budget:</span> $ {{ movie.budget }}</p>
        <p class="movie-fact">
          <span>Revenue:</span>
          {{
            movie.revenue.toLocaleString('en-us', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>
        <p class="movie-fact"><span>Rating:</span> {{ movie.vote_average }}</p>
        <p class="movie-fact"><span>Overview:</span> {{ movie.overview }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  onBeforeMount,
  ref,
  useFetch,
  useMeta,
  useRoute,
} from '@nuxtjs/composition-api'
import axios from 'axios'

export default defineComponent({
  name: 'Id',
  setup() {
    const route = useRoute()
    const id = computed(() => route.value.params.id)
    const movie = ref<object>({})
    const loading = ref(false)
    const name = ref<string>('')

    // Fetch Data
    const { fetch, fetchState } = useFetch(async () => {
      const response = await axios.get(`${process.env.base_url}${id.value}${process.env.api_key}`)

      loading.value = await fetchState.pending
      movie.value = response.data
      name.value = response.data.title
    })

    // Call the method on every reload
    onBeforeMount(() => {
      fetch()
    })

    // Setup meta tag for SEO
    useMeta(() => ({ title: name.value }))

    return {
      movie,
      loading,
      name,
    }
  },
  // You need to define an empty head to activate this functionality
  head: {},
})
</script>
