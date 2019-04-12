<template>
  <div class="footer-overview">
    <div class="footer-menu">
      <a href="Javascript:void(0)" @click="$emit('update:movieFilter', 'popular')" 
        :class="{'active': movieFilter === 'popular'}">popular</a>
      <a href="Javascript:void(0)" @click="$emit('update:movieFilter', 'now playing')" 
        :class="{'active': movieFilter === 'now playing'}">now playing</a>
      <a href="Javascript:void(0)" @click="$emit('update:movieFilter', 'top rated')" 
        :class="{'active': movieFilter === 'top rated'}">top rated</a>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'FooterMenu',
    props: {
      movieFilter: {
        type: String,
        required: true,
      }
    }
  }  
</script>

<style type="text/css"></style>