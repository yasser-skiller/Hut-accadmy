<template >
 <b-container class="my-4" >
  <div v-if="status_code === 200 ">
     <div class="d-flex justify-content-end">
        <p class="">السؤال رقم <span class="OrangeColor">{{Quiz_serial+1}}</span> من <span class="GreenColor">{{Quiz_data.questions.length}}</span> سؤال</p>
      </div>
      <p class="text-center DarkBlueSecColor">{{Quiz_duration}}</p>
      <p class="DarkBlueSecColor f-14"> {{Quiz_data.name}}</p>
      <p v-html="Quiz_data.questions[Quiz_serial].content"></p>

        <b-row align-h="center"  class="flex-wrap-reverse  justify-content-center align-items-end">
          <b-col cols="11" sm="10"  md="8" lg="6" class="px-3 my-4">
            <b-form-group  v-slot="{ ariaDescribedby }"  >
            <div v-for="option in Quiz_data.questions[Quiz_serial].options" :key="option.uid">
              <label
               :class="selected === `{'${Quiz_data.questions[Quiz_serial].id}' : {'answered' : '${option.value}'}}` ? 'selected rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options' : 'rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options'"
               :for="option.uid">{{option.title}}
              </label>
              <b-form-radio
                v-model="selected"
                :aria-describedby="ariaDescribedby"
                :id="`${option.uid}`"
                class="d-none"
                name="some-radios"
                :value="`{'${Quiz_data.questions[Quiz_serial].id}' : {'answered' : '${option.value}'}}`"
              >
              </b-form-radio>
            </div>
            </b-form-group>

            <div class="mt-3">Selected: <strong>{{ selected }}</strong></div>
            <div class="w-100 d-flex justify-content-center">
              <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial === Quiz_data.questions.length-1 " v-on:click="SendData">إنهاء الاختبار</b-button>
              <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial !== Quiz_data.questions.length-1 " v-on:click="Next">التالي</b-button>
              <b-button type="button" size="lg" variant="outline-danger" class="p mr-5 rounded f-14" v-if="Quiz_serial > 0 " v-on:click="Quiz_serial--">السابق</b-button>
            </div>

          </b-col>

          <b-col cols="11" sm="10"  md="8" lg="6" class="px-3 my-4 scrollManger rounded bg-DarkGrayColor">
            <div class="scrollManger">
                <p class="GraySecColor text-center my-3">سؤال بنص موجود</p>
                <p class="GraySecColor f-14" v-html="Quiz_data.content"></p>
            </div>

          </b-col>

        </b-row>
  </div>

  <div v-else class="d-flex justify-content-center align-items-center spinner_loading">
    <Loading/>
  </div>

  </b-container>
</template>

<script>
import axios from "axios";
import config from "@/config";
import Loading from "@/components/local/Loading";
  export default {
      components:{
        Loading,
      },
    data() {
      return {
        Quiz_data: [],
        Answered:{},
        selected: '',
        status_code: 0,
        Quiz_serial:0,
        Quiz_duration:0,
        Minute:0,
        Seconds:0,
        Remseconds:0,

      }
    },
    mounted() {
      this.fetchData();
      this.SendData();
    },
    methods: {
     async fetchData() {
      try {
        const res = await axios.get(
          `${config.apiUrl}wp-json/learnpress/v1/quiz/95`,
            { headers: {"Authorization" : `Bearer ${config.token}`} },
            { auth: {"username" : `${config.username}`, "password": `${config.password}`} }
        );
        this.status_code = res.status
        this.Quiz_data = res.data;
        this.Seconds =  res.data.duration.replace(/minutes/g,'')*60,
        console.log(this.Quiz_data);
      } catch (error) {
        console.log(error);
      }
     },
      SendData() {
        axios.post(
          `${config.apiUrl}wp-json/learnpress/v1/quiz/finish`,
          { headers: {"Authorization" : `Bearer ${config.token}`} },
          { auth: {"username" : `${config.username}`, "password": `${config.password}`, "id" : 95, "answered": {"96" : {"answered" : "99fca2db"},}} }
        )
        .then((res) => {
          console.log(res);
        })
        .catch((error) => {
          console.log(error);
        });
      },
      Next(){
        this.Quiz_serial++ ;
        this.Answered.push(this.selected);
        this.selected = '';
        console.log(this.Answered);
        console.log(this.selected);
      }


    },
     watch: {
      Seconds: {
        handler(value) {
          if (value > 0) {
            setTimeout(() => {
              this.Minute = Math.floor(this.Seconds / 60)
              this.Remseconds = this.Seconds % 60
              this.Seconds--;
              if(this.Seconds < 9){
                this.Quiz_duration = this.Minute + ':0' + this.Remseconds
              }if(this.Minute < 9){
                this.Quiz_duration = "0"+this.Minute + ':' + this.Remseconds
              }else{
                this.Quiz_duration = this.Minute + ':' + this.Remseconds
              }
            }, 1000);
          }
        },
        immediate: true
      },

    }

  }
</script>


<style scoped>
.scrollManger{
  max-height: 350px;
  overflow-y: auto;
}
.spinner_loading{
  height: 90vh;
}
.selected{
  border: 2px solid var(--BlueColor);
}
</style>
