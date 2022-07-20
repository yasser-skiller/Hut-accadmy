<template>
  <b-row align-h="center"  class="justify-content-between align-items-center">
    <b-col cols="12" sm="12"  md="4" lg="3" :class="Mood === true ? 'sideMenu bg-GrayForColor' : 'sideMenuHidden bg-GrayForColor' ">
      <div class="d-flex justify-content-end mt-3">
        <img :src="require(`~/assets/img/right-arrow.png`)"  class="arrow cursor" v-on:click="sideMenu"/>
      </div>
    </b-col>
    <b-col cols="12" sm="12"  md="8" lg="8" class="border_0 mx-auto">
      <VideoPlayer
        src="https://www.youtube.com/watch?v=CwROcBKXsus"
        class=""
      />
    </b-col>
  </b-row>
</template>

<script>
import VideoPlayer from 'nuxt-video-player'

require('nuxt-video-player/src/assets/css/main.css')
export default {
  components:{
    VideoPlayer
  },
  data() {
      return {
        Mood: true,
      }
    },
  methods: {
    sideMenu(){
      this.Mood = !this.Mood
    },
  },

}
</script>

<style scoped>
.sideMenu{
  height: 90vh;
  overflow-y: auto;
}
.arrow{
  width: 18px;
}
.sideMenuHidden{
  height: 40px;
  position: absolute;
  top: 100px;
  right: 0px;
  width: 50px;
}
</style>
