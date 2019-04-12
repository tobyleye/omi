<template>
  <div class="movie-details-modal" v-if="active">
    <div class="modal-overlay"></div>
      <div class="modal-content" @scroll="scrolling">
        <Spinner v-if="isMovieDetailsLoading"/>
        <div v-if="!isMovieDetailsLoading && !movieDetailsFailed">
          <div class="hero">
            <div class="movie-backdrop" :style="backdropCss"></div>
            <div :class="{'show-header': showHeader}" class="app-header">
              <div class="arrow-back header-ctrl" @click="$emit('update:active', false)"></div>
            </div>
          </div>
          <div class="movie movie-content">
            <div class="intro">
              <div class="poster">
                <img :src="posterSrc">
              </div>
              <div style="flex: 1; padding-top: 16px;">
                <div class="title">{{ movieDetails.title }}</div>
                <div class="release-date">{{ releaseDate }}
                  <span class="status">{{ movieDetails.status }}</span>
                </div>
                <div class="runtime">{{ runtime }}</div>
                <div class="genres">{{ genres }}</div>
              </div>
            </div>
            <div class="meta section" style="margin-top: 20px;">
              <div>
                <span class="field">Language</span>
                <span class="value">{{ spokenLanguages }}</span>
              </div>
              <div>
                <span class="field">Rating</span>
                <span class="value">{{ movieDetails.vote_average }}<span style="font-size: 12px">/10</span></span>
              </div>
              <div>
                <span class="field">Budget</span>
                <span class="value">${{ budget }}</span>
              </div>
            </div>
            <div class="storyline section">
              <h3>Story line</h3>
              <p>{{ movieDetails.overview }}</p>
            </div>
            
            <div class="cast section">
            <h3>The Cast <span style="font-weight: 400; font-size: 12px; color: #9E9E9E; margin-left: 6px">(Click on image to see character name)</span></h3>
              <div class="cast-horizontal-scroll">
                <MovieCast 
                  v-for="cast in movieDetails.cast" 
                  :key="cast.id"
                  :cast="cast"
                />
              </div>
           </div>
           <div class="trailer section">
            <h3>Trailer</h3>
            <div>
              <div class="embedded-player" 
                v-for="trailer in movieDetails.trailers"
                :key="trailer.id">
                <iframe width="100%" height="100%"
                  :src="`https://youtube.com/embed/${trailer.key}`" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Spinner from './Spinner.vue';
  import MovieCast from './MovieCast.vue';

  export default {
    name: 'MovieDetails',
    components: {
      Spinner,
      MovieCast  
    },
    props: {
      movieDetails: {
        type: Object,
        required: true
      },
      isMovieDetailsLoading: {
        type: Boolean, 
        required: true,  
      },
      active: {
        type: Boolean,
        isRequired: true,
      },
      movieDetailsFailed: Boolean,
    },
    data: () => ({showHeader: false}),

    computed: {
      budget() {
        const { budget } = this.movieDetails;
        if (budget > 1e6) return `${(budget/1e6).toFixed(0)}M`
        return `${(budget/1e3).toFixed(0)}K`
      },
      posterSrc() {
        return `http://image.tmdb.org/t/p/w185/${this.movieDetails.poster_path}`
      },  
      spokenLanguages() {
        return this.movieDetails.spoken_languages[0].name
      },
      backdropCss() {
        const baseUrl = 'http://image.tmdb.org/t/p';
        const posterSize = 'original';
        const src = [baseUrl, posterSize, this.movieDetails.backdrop_path].join('/')
        return `background-image: url(${src})`
      },
      genres() {
        return this.movieDetails.genres
          .map(genre => genre.name)
          .join(', ')
      },
      releaseDate() {
        const months = ['January', 'Febuary', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
        const release_date = new Date(this.movieDetails.release_date);
        return `${months[release_date.getMonth()]} ${release_date.getDate().toString().padStart(2, 0)}, ${release_date.getFullYear()}`
      },
      runtime() {
        const { runtime } = this.movieDetails;
        const hr = Math.floor(runtime/60)
        const min = runtime - (hr * 60);
        return `${hr}${hr > 1 ? 'hrs' : 'hr'} ${min}min`
      }
    },
    methods: {
      getCharacterAvatar(profile_path) {
        return `http://image.tmdb.org/t/p/w185/${profile_path}`
      },
      scrolling(evt) {
        if (evt.target.scrollTop > 300) {
          this.showHeader = true;
        } else {
          this.showHeader = false;
        }
      }
    }
  }
</script>