<template>
<div>
  <b-row align-h="center"  class="justify-content-center align-items-center">
    <b-col  cols="11" sm="10"  md="6" lg="6" class="mx-auto my-3">
      <div class="">
        <h1 class="my-4 text-center font-weight-bold DarkWhiteColor">
          اكاديمية الحوت
        </h1>
        <p class="mt-2 px-5 text-center DarkWhiteColor">
          هذا النص هو مثال لنص يمكن أن يستبدل في نفس المساحة ق.
        </p>
        <p class="px-5 text-center DarkWhiteColor">
      في نفس ال تم توليد هذا النص من مولد النصأو العديد من النصوص الأخرى إضافة إلى زيادة عدد الحروف التى يولدها التطبيق.
        </p>
        <div class="d-flex flex-wrap mt-4 justify-content-center">
            <b-button size="sm" class="my-2 my-sm-2 mx-2 py-2 px-5 rounded" type="button" variant="primary" >تسجيل دخول</b-button>
            <b-button size="sm" class="my-2 my-sm-2 mx-2 py-2 px-5 rounded" type="button" variant="success">انشاء حساب</b-button>
        </div>


      </div>
    </b-col>
    <b-col  cols="11" sm="10"  md="6" lg="6" class="bg-img">
      <img  :src="require(`~/assets/img/header_logo.png`)" lat="header_logo" class="header_logo"/>
    </b-col>


  </b-row>
</div>
</template>


<style scoped>
.bg-img{
  position: relative;
  background-image: url('~/assets/img/header2.png');
  background-position: center;
  background-clip: content-box;
  background-repeat: no-repeat;
  background-size: cover;
  height: 95vh;
  color: var(--WhiteColor);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.header_logo{
  width: 65%;
}
</style>
