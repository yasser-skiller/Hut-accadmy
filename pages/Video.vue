<template>
 <div>
    <AppNav/>
    <Video />
 </div>
</template>

<script>
import AppNav from '@/components/Global/AppNav';
import Video from '@/components/local/Video';

require('nuxt-video-player/src/assets/css/main.css')
export default {
  components:{
    AppNav,
    Video,
  },

}
</script>
