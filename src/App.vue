<template>
  <div id="app">
    <AppHeader/>
    <main class="main">
      <div class="app-content" style="height: 100%;">
        <Spinner v-if="isMovieListLoading" />
        <MovieList
          v-else
          :movie-list="movieList"
          @onClickMovieName="getMovieDetails"/>
         <button @click="loadMoreMovies" v-show="!isMovieListLoading && movieList.length !== 0" class="btn-load">Load more 🤤</button>
      </div>
    </main>
    <FooterMenu :movieFilter.sync="movieFilter"/>
    <MovieDetails 
      :isMovieDetailsLoading="isMovieDetailsLoading" 
      :movieDetails="movieDetails" 
      :active.sync="isMovieModalActive" 
      :movieDetailsFailed="movieDetailsFailed"/>
    <transition name="slide-up">
      <div :class="`toast ${toast.type}`" v-if="toast">{{ toast.message }}</div>
    </transition>
  </div>
</template>

<script>
  import AppHeader from './components/AppHeader.vue';
  import MovieList from './components/MovieList.vue';
  import MovieDetails from './components/MovieDetails.vue';
  import FooterMenu from './components/FooterMenu.vue';
  import Spinner from './components/Spinner.vue';

  import { API_KEY, API_ROOT } from './constants.js';

  export default {
    name: 'app',
    components: {
      AppHeader,
      MovieList,
      MovieDetails,
      FooterMenu,
      Spinner
    },
    data: () => ({
      isMovieModalActive: false,
      showAppDetails: false,
      movieList: [],
      isMovieListLoading: false,
      isMovieDetailsLoading: false,
      movieDetails: {}, 
      movieFilter: 'popular',
      movieDetailsFailed: false,
      toast: undefined,
    }),
    watch: {
      async movieFilter() {
        this.movieList = [];
        this.isMovieListLoading = true;
        const url = `${API_ROOT}/movie/${this.movieFilter.replace(' ', '_')}?api_key=${API_KEY}&language=en-US&page=1`
        try {
          const response = await fetch(url).then(res => res.json());
          this.movieList = response.results
        } catch(e) {
          this.showToast({
            message: 'Network error 😢',
            type: 'error'
          });
        }
        this.isMovieListLoading = false 
      } 
    },
    async created() {
      this.isMovieListLoading = true;
      const url = `${API_ROOT}/movie/${this.movieFilter.replace(' ', '_')}?api_key=${API_KEY}&language=en-US&page=1`
      try {
        const response = await fetch(url).then(res => res.json());
        this.movieList = response.results
      } catch (e) {
        this.showToast({
          message: 'Network error 😢',
          type: 'error'
        });
      }
      this.isMovieListLoading = false
    },
    // :end 
    methods: {
      async getMovieDetails(evt) {
        const { movieId } = evt
       
        this.movieDetailsFailed = false;
        this.isMovieModalActive = true;
        this.isMovieDetailsLoading = true

        try {
          const movieDetails = await fetch(`https://api.themoviedb.org/3/movie/${movieId}?api_key=${API_KEY}&language=en-US`).then(res => res.json()); 
          const movieCast = await fetch(`${API_ROOT}/movie/${movieId}/credits?api_key=${API_KEY}`).then(res => res.json());
          const movieTrailer = await fetch(`https://api.themoviedb.org/3/movie/${movieId}/videos?api_key=${API_KEY}&language=en-US`).then(res => res.json());
          movieDetails.cast = movieCast.cast.slice(0, 8);
          movieDetails.trailers = movieTrailer.results
            .filter(trailer => trailer.type === 'Trailer')
            .slice(0, 2); // not more than 2 trailers;
          this.movieDetails = movieDetails;
          this.isMovieDetailsLoading = false;
        } catch(e) {
          this.movieDetailsFailed = true
          this.isMovieModalActive = false
          this.showToast({
            message: 'Network error 😢',
            type: 'error'
          });
        }
      },
      loadMoreMovies() {
        // TODO:
        this.showToast({
          message: 'Feature not implemented yet 🤭',
          type: 'info'
        })
      },
      showToast({message, type, delay=3500}) {
        this.toast = {message, type}
        setTimeout(() => {
          this.toast = undefined;
        }, delay)
      }
    }
  }
</script>

<style>
  @import './assets/styles/main.css'
</style>
