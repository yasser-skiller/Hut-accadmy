<template>
 <div>
    <div class="containerMode">
      <div class="darkMode"></div>
    </div>
    <AppNav/>
    <Header/>
    <WE/>
    <QuduratSec/>
    <Tahasuli/>
    <Alnamudhaj/>
    <Contact/>
    <LastNews/>
 </div>
</template>

<script>
import AppNav from '@/components/Global/AppNav';
import Header from '@/components/local/Header';
import WE from '@/components/local/WE';
import QuduratSec from '@/components/local/QuduratSec';
import Tahasuli from '@/components/local/Tahasuli';
import Alnamudhaj from '@/components/local/Alnamudhaj';
import Contact from '@/components/local/Contact';
import LastNews from '@/components/Global/LastNews';

export default {
  components:{
    AppNav,
    Header,
    WE,
    QuduratSec,
    Tahasuli,
    Alnamudhaj,
    Contact,
    LastNews,
  },

}
</script>
