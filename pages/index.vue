<template>
  <Loading v-if="!loading" />
  <div v-else class="home">
    <!-- Hero -->
    <Hero />
    <!-- Search -->
    <div class="container search">
      <input v-model.lazy="search" type="text" placeholder="Search" @keyup.enter="searchMovies" />
      <button
        v-show="search !== ''"
        class="button animate__animated animate__zoomIn"
        @click="clearSearch"
      >
        Clear
      </button>
    </div>

    <!-- Movies -->
    <div class="container movies">
      <!-- Now Playing Movies -->
      <div v-if="search === ''" id="movies" class="movies-grid">
        <div
          v-for="(movie, index) in movies"
          :key="index"
          class="movie animate__animated animate__zoomIn"
        >
          <NuxtLink :to="{ name: 'movie-id', params: { id: movie.id } }">
            <div v-if="movie.poster_path !== null" class="movie-img">
              <img :src="`https://image.tmdb.org/t/p/w1280/${movie.poster_path}`" alt="movie" />
              <p class="overview">{{ movie.overview }}</p>
            </div>
          </NuxtLink>
          <div class="info">
            <p class="title">
              {{ movie.title }}
            </p>
            <p
              :class="{
                good_review: movie.vote_average >= 7,
                bad_review: movie.vote_average < 7,
              }"
            >
              {{ movie.vote_average }}⭐
            </p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
          </div>
        </div>
      </div>

      <!-- Searched Movies -->
      <div v-else-if="searchedMovies.length > 0" id="movies" class="movies-grid">
        <div
          v-for="(movie, index) in searchedMovies"
          :key="index"
          class="movie animate__animated animate__zoomIn"
        >
          <NuxtLink :to="{ name: 'movie-id', params: { id: movie.id } }">
            <div class="movie-img">
              <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="movie" />
              <p class="overview">{{ movie.overview }}</p>
            </div>
          </NuxtLink>
          <div class="info">
            <p class="title">
              {{ movie.title }}
            </p>
            <p
              :class="{
                good_review: movie.vote_average >= 7,
                bad_review: movie.vote_average < 7,
              }"
            >
              {{ movie.vote_average }} ⭐
            </p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
          </div>
        </div>
      </div>
      <div v-else class="notFound">
        <h1 class="notFound">No movies found 😢</h1>
      </div>
    </div>
    <Footer />
  </div>
</template>

<script lang="ts">
import { defineComponent, onBeforeMount, ref, useFetch, useMeta } from '@nuxtjs/composition-api'
import axios from 'axios'
import Swal from 'sweetalert2'
import Movie from '../interfaces/Movie'

export default defineComponent({
  name: 'Index',
  setup() {
    const movies = ref<Movie[]>([])
    const searchedMovies = ref<Movie[]>([])
    const loading = ref<boolean>(false)
    const searchReq = ref<boolean>(false)
    const search = ref<string>('')

    // Fetch Data
    const { fetch, fetchState } = useFetch(async () => {
      const response = await axios.get(`${process.env.now_playing}`)
      loading.value = await fetchState.pending

      response.data.results.forEach((movie: Movie) => {
        return movies.value.push(movie)
      })
    })

    // Call the method on every reload
    onBeforeMount(() => {
      fetch()
    })

    // call the search when the button is clicked
    const searchMovies = async () => {
      // Fetch the Searched Movies
      try {
        const filterResponse = await axios.get(`${process.env.search_url}${search.value}`)
        searchedMovies.value = filterResponse.data.results.filter(
          (movie: Movie) => movie.poster_path !== null
        )
        if (searchedMovies.value.length === 0) {
          Swal.fire({
            icon: 'error',
            title: 'No Movies Found with this Name',
          })
        }
      } catch (error) {
        Swal.fire({
            icon: 'error',
            title: `${error}`,
          })
      } finally {
        searchReq.value = true
      }
    }

    const clearSearch = () => {
      search.value = ''
      searchedMovies.value = []
    }

    // Setup meta tag for SEO
    // This set the title in head - but won't allow you to read its state outside of this component.
    useMeta({
      title: 'Movies',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest streaming movies in theaters & online',
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, streaming',
        },
      ],
      script: [
        {
          hid: 'sweetalert2',
          src: 'https://cdnjs.cloudflare.com/ajax/libs/sweetalert/2.1.2/sweetalert.min.js',
          defer: true,
        },
      ],
    })

    return {
      loading,
      movies,
      search,
      searchedMovies,
      searchMovies,
      clearSearch,
      searchReq
    }
  },
  // You need to define an empty head to activate this functionality
  head: {},
})
</script>
