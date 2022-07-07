<template >
 <b-container class="my-4" >
  <div v-if="status_code === 200 ">
     <div class="d-flex justify-content-end">
        <p class="">السؤال رقم <span class="OrangeColor">{{Quiz_serial+1}}</span> من <span class="GreenColor">{{Quiz_data.questions.length}}</span> سؤال</p>
      </div>
      <p class="text-center DarkBlueSecColor">{{Quiz_duration}}</p>
      <div class="d-flex w-100 my-5">
        <div class="" v-for="index in Quiz_data.questions.length" :key="index">
        <span
          v-on:click="Pagination(index)"
          :id="`${Quiz_data.questions[index-1].id}`"
          class="py-2 px-3 mx-1 Pagination_label bg-RevsionColor"
          >
          {{index}}
          </span>
        </div>
      </div>

      <p class="DarkBlueSecColor f-14"> {{Quiz_data.name}}</p>
      <p v-html="Quiz_data.questions[Quiz_serial].content"></p>

      <b-row align-h="center"  class="flex-wrap-reverse  justify-content-center align-items-end">
        <b-col cols="11" sm="10"  md="8" lg="6" class="px-3 my-4">
          <b-form-group  v-slot="{ ariaDescribedby }"  >
          <div v-for="option in Quiz_data.questions[Quiz_serial].options" :key="option.uid">
            <label
            :class="selected === `{'${Quiz_data.questions[Quiz_serial].id}' : {'answered' : '${option.value}'}}` ? 'selected rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options' : 'rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options'"
            :for="option.uid"
            v-on:click="Save(Quiz_data.questions[Quiz_serial].id, `{'${Quiz_data.questions[Quiz_serial].id}' : {'answered' : '${option.value}'}}`)"
            >
            {{option.title}}
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
            <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial === Quiz_data.questions.length-1 " v-on:click="Finish_Quiz">إنهاء الاختبار</b-button>
            <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial !== Quiz_data.questions.length-1 " v-on:click="Next">التالي</b-button>
            <b-button type="button" size="lg" variant="outline-danger" class="p mr-5 rounded f-14" v-if="Quiz_serial > 0 " v-on:click="Previous">السابق</b-button>
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
   <div v-if="status_code === 202" class="d-flex justify-content-center align-items-center spinner_loading">
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
        Answered:[],
        AnsweredObj:{},
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
      // this.SendData();
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
        this.Seconds =  res.data.duration.replace(/minutes/g,'')*60;

        // active
        setTimeout(() => {
          document.getElementById(this.Quiz_data.questions[this.Quiz_serial].id).classList.add('bg-CurrentColor')
        }, 500);

      } catch (error) {
        console.log(error);
      }

     },
      SendData() {
        axios.post(
          `${config.apiUrl}wp-json/learnpress/v1/quiz/finish`,
          { headers: {"Authorization" : `Bearer ${config.token}`} },
          { auth: {"username" : `${config.username}`, "password": `${config.password}`, "id" : 95, "answered": {...this.Answered} } }
        )
        .then((res) => {
          console.log(res);
          this.status_code = res.status;
        })
        .catch((error) => {
          console.log(error);
        });
      },
      Compare(){
        console.log("matches",this.Answered);
        const matches  = this.Answered.filter(element => {
          console.log("element",element)
          if(element !== undefined){
            if (element.indexOf(`{'${this.Quiz_data.questions[this.Quiz_serial].id}' : {'answered' : '`) !== -1) {
              return true;
            }
          }

        });
        this.selected = matches[0];

        // currnet
        let arr_Pagination_label = document.querySelectorAll('.Pagination_label');
        arr_Pagination_label.forEach(element => {
           element.classList.remove('bg-CurrentColor')
        });
        document.getElementById(this.Quiz_data.questions[this.Quiz_serial].id).classList.add('bg-CurrentColor')
      },
      Save(id, value){
        console.log("id",id);
        console.log("this.selected",value);
        if(this.Answered.length > 0){
          console.log("sav",this.Answered);
          this.Answered.forEach(element => {
            if(element){
              if(element.includes(`${id}'`)){
                this.Answered =  this.Answered.filter(e => e !== element);
              }
            }
          });
          this.Answered.push(value);
        }if(this.Answered.length === 0){
          this.Answered.push(value);
        }

        console.log("saving",this.Answered);
        // this.AnsweredObj = {...this.Answered}
        // console.log(this.AnsweredObj);
        // active Question
        document.getElementById(id).classList.add('bg-AnswerColor')

      },
      Next(){
        this.Quiz_serial++ ;
        this.Compare();
      },
      Previous(){
        this.Quiz_serial-- ;
        this.Compare();
      },
      Finish_Quiz(){
        this.SendData();
        this.status_code = 202;

      },
      Pagination(index){
        this.Quiz_serial = index -1;
        this.Compare();
      },


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
          }else{
            this.SendData();
            this.status_code = 202;
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
