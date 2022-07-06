<template >
    <b-navbar toggleable="lg"  class="nav" >

    <b-navbar-brand href="#">
      <div  class="d-flex align-items-center">
        <img :src="require(`~/assets/icon/logo.svg`)" class="logo" alt="icon"/>
        <div class="logoText">
          <span>أكاديمية الحوت</span>
          <span>Alhut Academy</span>
        </div>
      </div>
    </b-navbar-brand>


    <b-collapse id="nav-collapse" class="justify-content-around" is-nav>


      <b-navbar-nav class="NavBar text-center justify-content-around w-70  align-items-center">
        <NuxtLink  to="/" tag="li" exact class="nav-link">الرئيسية</NuxtLink >
        <NuxtLink to="#WE" class="nav-link"> من نحن</NuxtLink>
        <NuxtLink to="#Qudurat" class="nav-link">قدرات</NuxtLink>
        <NuxtLink to="#Tahasuli" class="nav-link">تحصيلي</NuxtLink>
        <NuxtLink to="#Contact" class="nav-link">الدعم الفني</NuxtLink>
        <NuxtLink to="#LastNews" class="nav-link"> اخر الاخبار</NuxtLink>
      </b-navbar-nav>

      <b-navbar-nav class="pr-0">

        <div class="d-flex">
          <img :src="require(`~/assets/img/person.png`)" class="rounded-circle person" alt="icon"/>
          <div class="mr-1 mt-1">
            <p class="personName my-0">محمد مصطفي علي</p>
            <p class="personTitle DarkBlueSecColor my-0">محمد1994</p>
          </div>
        </div>
      </b-navbar-nav>

    </b-collapse>

    </b-navbar>
</template>


<style scoped>
 .nav{
  min-height: 80px;
  border-bottom: 1px solid gray;
 }
 .logo{
  width: 55px;
  margin-left: 5px;
 }
 .logoText{
   display: flex;
   flex-direction: column;
   font-size: 10px;
 }
 .nav-link{
  color: var(--GrayNave) !important;
  cursor: pointer;
 }
 .nav-link:hover{
  color: var(--BlackColor) !important;
 }
 .nuxt-link-active{
   color: var(--GrayNaveActive) !important;
   position: relative;
   display: flex;
   flex-direction: column;
   align-items: center;
 }
 .NavBar .nuxt-link-active::after{
   content: url('~/assets/icon/whale.svg');
   position: absolute;
   bottom: -20px;
 }

.NavIcon{
  background-image: url('~/assets/img/menu.png') !important;
  width: 40px;
    height: 40px;
    background-size: contain;
    display: flex;
}
.person{
  width: 50px;
  height: 50px;
}
.personName{
  font-size: 14px;
}
.personTitle{
  font-size: 12px;
}
</style>
