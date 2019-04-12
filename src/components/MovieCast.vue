<template>
  <div @click="showCharacter=!showCharacter">
    <div class="avatar">
      <img :src="characterAvatar"/>
    </div>
    <p v-show="showCharacter">as<br>{{ cast.character }}</p>
    <p v-show="!showCharacter">{{ cast.name }}</p>
  </div>
</template>

<script>
  export default {
    name: 'MovieCast',
    props: {
      cast: {
        type: Object,
        required: true
      }
    },
    data: () => ({ showCharacter: false }), 
    computed: {
      characterAvatar() {
        return `http://image.tmdb.org/t/p/w185/${this.cast.profile_path}`
      }
    }
  }
</script>

<style></style>