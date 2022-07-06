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
   mounted() {
    this.df();

  },
  methods:{
    df(){
                    console.log("sdsd");
      // var data = JSON.stringify({"username":"root","password":"yasser12!@"});

      //     var xhr = new XMLHttpRequest();
      //     xhr.withCredentials = true;

      //     xhr.addEventListener("readystatechange", function() {
      //       if(this.readyState === 4) {
      //         console.log(this.response);

      //       }
      //     });

      //     xhr.open("GET", "http://localhost/wordpress/wp-json/learnpress/v1/quiz/128");
      //     xhr.setRequestHeader("Authorization", "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9sb2NhbGhvc3RcL3dvcmRwcmVzcyIsImlhdCI6MTY1Njk3MDk3NSwibmJmIjoxNjU2OTcwOTc1LCJleHAiOjE2NTc1NzU3NzUsImRhdGEiOnsidXNlciI6eyJpZCI6IjEifX19.4c6RbEO29-pLrfpy9Nh08yi5L3z74S47VHX435OCN70");
      //     xhr.send(data);
    },
  }
}
</script>
