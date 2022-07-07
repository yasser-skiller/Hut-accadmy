<template>
 <div>
    <div class="containerMode">
      <div class="darkMode"></div>
    </div>
    <AppNav/>
    <Quiz/>
 </div>
</template>

<script>
import AppNav from '@/components/Global/AppNav';
import Quiz from '@/components/local/Quiz';

export default {
  components:{
    AppNav,
    Quiz,
  },

}
</script>
