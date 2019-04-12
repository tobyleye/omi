<template>
  <li>
    <div class="movie-card">
      <div class="movie-poster">
        <img :alt="movie.title" :src="getMoviePosterSrc">
      </div>
      <div class="movie-details">
        <div class="header">
          <div class="movie-title">
            <a href="javascript:void(0)" @click="onClickMovieName(movie.id)">{{ shorten(movie.title, 16) }}</a>
          </div>
          <div class="movie-rating">{{ movie.vote_average.toFixed(1) }}</div>
        </div>
        <div class="movie-overview">
          <a href="javascript:void(0)" @click="onClickMovieName(movie.id)">{{ shorten(this.movie.overview, 70) }}</a>
        </div>
        <div class="release-date">{{ releaseDate }}</div>
      </div>
    </div>
  </li>
</template>

<script>
  export default {
    name: 'MovieCard',
    props: {
      movie: {
        type: Object,
        required: true
      }
    },
    computed: {
      getMoviePosterSrc() {
        const baseUrl = 'http://image.tmdb.org/t/p';
        const posterSize = 'w185';
        return [baseUrl, posterSize, this.movie.poster_path].join('/')
      },
      releaseDate() {
        const months = ['January', 'Febuary', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
        const release_date = new Date(this.movie.release_date);
        return `${months[release_date.getMonth()]} ${release_date.getDate().toString().padStart(2, 0)}, ${release_date.getFullYear()}`
      }
    },
    methods: {
      shorten(string, maxChar) {
        if (string.length > maxChar) {
          return string.slice(0, maxChar) + '...'
        }
        return string.slice(0, maxChar);  
      },
      onClickMovieName(movieId) {
        this.$emit('onClickMovieName', { movieId })
      }
    } 
  }
</script>

<style></style>