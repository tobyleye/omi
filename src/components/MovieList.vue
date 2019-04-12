<template>
  <ul class="movie-list">
    <movie-card v-for="movie in movieList" :movie="movie" :key="movie.id"
      @onClickMovieName="$emit('onClickMovieName', $event)">
    </movie-card>
  </ul>
</template>

<script>
  import MovieCard from './MovieCard.vue';

  export default {
    name: 'MovieList',
    props: {
      movieList: {
        type: Array,
        required: true,
      }
    },
    components: {
      MovieCard
    }
  }
</script>

<style></style>