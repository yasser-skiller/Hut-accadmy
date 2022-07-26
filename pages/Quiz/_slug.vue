<template >
<div>
  <AppNav/>
  <b-container class="my-4" >
  <div v-if="status_code === 'success' && this.Quiz_data.length > 0 ">
     <div class="d-flex justify-content-end">
        <p class="">السؤال رقم <span class="OrangeColor">{{Quiz_serial+1}}</span> من <span class="GreenColor">{{Quiz_data.length}}</span> سؤال</p>
      </div>
      <p class="text-center DarkBlueSecColor">{{Quiz_duration}}</p>
      <div class="d-flex flex-wrap w-100 mt-5 mb-4">
        <div class="my-3 mx-2" v-for="index in Quiz_data.length" :key="index">
        <span
          v-on:click="Pagination(index)"
          :id="`${Quiz_data[index-1].id}`"
          class="py-2 px-3 mx-1 Pagination_label border"
          >
          {{index}}
          </span>
        </div>
      </div>

      <div class="d-flex align-items-center mb-4 flex-wrap">
        <span class="f-14">محلول</span>
        <span class="box bg-AnswerColor mx-2"></span>
        <span class="f-14">مؤجل</span>
        <span class="box bg-RevsionColor  mx-2"></span>
        <span class="f-14">الحالي</span>
        <span class="box bg-CurrentColor  mx-2"></span>

      </div>

      <p class="DarkBlueSecColor f-14"> {{Quiz_data[Quiz_serial].title}}</p>
      <p v-html="Quiz_data[Quiz_serial].content"></p>

      <b-row align-h="center"  class="flex-wrap-reverse  justify-content-center align-items-end">
        <b-col :class="Quiz_data[Quiz_serial].paragraph || Quiz_data[Quiz_serial].thumbnail ? 'col-11 col-lg-6 col-md-8 col-sm-6 px-3 my-4' : 'col-11 px-3 my-4'">
          <b-form-group  v-slot="{ ariaDescribedby }">
          <div :class="Quiz_data[Quiz_serial].paragraph || Quiz_data[Quiz_serial].thumbnail ? 'Responsive' : 'Responsive4' ">
             <div v-for="option in Quiz_data[Quiz_serial].options" :key="option.uid">
                <label
                :class="selected === option.value && Check_Answered_index.includes(Quiz_serial) === false ? `${option.uid} selected rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options text-dark` : `${option.uid} text-dark rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options`"
                :for="option.uid"
                v-on:click="Save(Quiz_data[Quiz_serial].id, option.value, Quiz_serial)"
                >
                {{option.title}}
                </label>
                <b-form-radio
                  v-model="selected"
                  :aria-describedby="ariaDescribedby"
                  :id="`${option.uid}`"
                  class="d-none"
                  name="some-radios"
                  :value="option.value"
                >
                </b-form-radio>
              </div>
          </div>

          </b-form-group>

          <div class="mt-3 d-none">Selected: <strong>{{ selected }}</strong></div>
          <div class="w-100 d-flex justify-content-center">
            <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial === Quiz_data.length-1 " v-on:click="Finish_Quiz">إنهاء الاختبار</b-button>
            <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary"  v-if="Quiz_serial !== Quiz_data.length-1 " v-on:click="Next">التالي</b-button>
            <b-button type="button" size="lg" class="p rounded f-14 mx-3" variant="outline-danger" v-if="Quiz_serial > 0 " v-on:click="Previous">السابق</b-button>
          </div>
          <div class="w-100 d-flex justify-content-center">
            <p class="GreenColor mt-3 cursor text-underline"  v-on:click="Pass">تأجيل السؤال</p>
          </div>

        </b-col>

        <b-col cols="11" sm="10"  md="8" lg="6" v-if="Quiz_data[Quiz_serial].paragraph" :class="Quiz_data[Quiz_serial].paragraph ? 'px-3 my-4 scrollManger rounded bg-DarkGrayColor' : 'd-none'">
          <div class="scrollManger">
              <p class="GraySecColor text-center my-3" v-html="Quiz_data[Quiz_serial].paragraph.title"></p>
              <p class="GraySecColor f-14" v-html="Quiz_data[Quiz_serial].paragraph.content"></p>
          </div>

        </b-col>

        <b-col cols="11" sm="10"  md="8" lg="6" v-if="Quiz_data[Quiz_serial].thumbnail" :class="Quiz_data[Quiz_serial].thumbnail ? 'px-3 my-4 ' : 'd-none'">
          <div class="scrollManger">
              <img :src="Quiz_data[Quiz_serial].thumbnail" alt="img" class="rounded ImgManger"/>
          </div>

        </b-col>

      </b-row>
  </div>

  <div v-else class="d-flex justify-content-center align-items-center spinner_loading">
    <Loading/>
  </div>


  </b-container>
</div>

</template>

<script>
import config from "@/config";
import Loading from "@/components/local/Loading";
import AppNav from '@/components/Global/AppNav';

  export default {
    components:{
      Loading,
      AppNav
    },
    data() {
      return {
        Quiz_data: [],
        Answered:[],
        last_answer:[],
        CHECK_ANSWER_arr:[],
        Check_Answered_Result : [],
        Check_Answered_index : [],
        selected: '',
        status_code: '',
        Quiz_serial:0,
        Quiz_duration:0,
        Minute:0,
        Seconds:0,
        Remseconds:0,
        fliter_uid: [],
      }
    },
    mounted() {
      this.fetchData();
    },
    methods: {
      fetchData() {
        var myHeaders = new Headers();
        myHeaders.append("Authorization", `Bearer${config.token}`);
        myHeaders.append("Content-Type", "application/json");

        var raw = JSON.stringify({"id":this.$route.params.slug});

        var requestOptions = {
          method: 'POST',
          headers: myHeaders,
          body: raw,
          redirect: 'follow'
        };

        fetch(config.apiUrl+"wp-json/learnpress/v1/quiz/start", requestOptions)
        .then(response => response.text())
        .then(res => {
          console.log(JSON.parse(res))
          this.Quiz_data = JSON.parse(res).results.questions;
          this.Seconds =  JSON.parse(res).results.duration;
          this.status_code = JSON.parse(res).status
          console.log(this.Quiz_data)
          console.log(this.Quiz_data[this.Quiz_serial])
          // active
          setTimeout(() => {
            document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('bg-CurrentColor')
          }, 2000);
        })
        .catch (error => console.log(error));
      },
      Check_Answered_View(){
        let options = document.querySelectorAll('.options');
        if(this.Check_Answered_Result !== undefined){
          options.forEach(ele => {
            ele.classList.add('bg-OpacityRedColor')
            this.Check_Answered_Result.forEach(option => {
              if(option.is_true === "yes"){
                this.fliter_uid.push(option.uid)
                if(ele.classList.contains(option.uid)){
                  ele.classList.add('bg-OpacityGreenColor')
                }
              }

            });
          })
        }

      },
      CHECK_ANSWER() {
        var myHeaders = new Headers();
        myHeaders.append("Authorization", `Bearer${config.token}`);
        myHeaders.append("Content-Type", "application/json");


        var raw = JSON.stringify({
          "id":this.$route.params.slug,
          "question_id" : this.Quiz_data[this.CHECK_ANSWER_arr[1]].id,
          "answered" : this.CHECK_ANSWER_arr[0]
        });

        var requestOptions = {
          method: 'POST',
          headers: myHeaders,
          body: raw,
          redirect: 'follow'
        };

        fetch(config.apiUrl+"wp-json/learnpress/v1/quiz/check_answer", requestOptions)
          .then(response => response.text())
          .then(res => {
            this.Check_Answered_Result = JSON.parse(res).options;
            this.CHECK_ANSWER_arr = [];
            this.Check_Answered_View();
          })
          .catch(error =>{
            this.CHECK_ANSWER_arr = [];
            console.log('error', error)
          });


      },
      Compare(){
        if(this.Answered.length > 0){
          this.Answered.forEach(element => {
            if(element.my_Quiz_serial === this.Quiz_serial){
              this.selected = element.answer
            }
          });
        }


        // currnet
        let arr_Pagination_label = document.querySelectorAll('.Pagination_label');
        arr_Pagination_label.forEach(element => {
          element.classList.remove('bg-CurrentColor')
        });
        document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('bg-CurrentColor')

        // check
        setTimeout(() => {
          if(this.Check_Answered_index.includes(this.Quiz_serial)){
            let options = document.querySelectorAll('.options');
            options.forEach(ele => {
              ele.classList.add('bg-OpacityRedColor')
              this.fliter_uid.forEach(uid => {
                if(ele.classList.contains(uid)){
                  ele.classList.add('bg-OpacityGreenColor')
                }
              });
            })
          }

        }, 500);

      },
      Save(id, option_value, my_Quiz_serial){
        console.log(this.Check_Answered_index.includes(my_Quiz_serial))
        if(this.Check_Answered_index.includes(my_Quiz_serial) === false){
          if(this.Answered.length > 0){
            this.Answered.forEach(element => {
              if(element){
                if(element.id === id){
                  this.Answered =  this.Answered.filter(e => e !== element);
                }
              }
            });
            this.Answered.push({'id':id,'answer':option_value,'my_Quiz_serial':my_Quiz_serial});
          }if(this.Answered.length === 0){
            this.Answered.push({'id':id,'answer':option_value,'my_Quiz_serial':my_Quiz_serial});
          }

          document.getElementById(id).classList.add('bg-AnswerColor');

          // check answer

          this.last_answer.push(option_value)
          this.last_answer.push((new Date().getMinutes() * 60) + new Date().getSeconds())
          if(this.last_answer.length === 4){
            if(this.last_answer[0] === this.last_answer[2]){
              if(this.last_answer[3] - this.last_answer[1] > 0.5){
                if(this.last_answer[3] - this.last_answer[1] < 5 ){
                  this.CHECK_ANSWER_arr.push(this.last_answer[2],my_Quiz_serial);
                  this.CHECK_ANSWER();
                  this.Check_Answered_index.push(my_Quiz_serial)
                }
              }
            }else{
              this.last_answer = []
              this.last_answer.push(option_value)
              this.last_answer.push((new Date().getMinutes() * 60) + new Date().getSeconds() )
            }
          }
          if(this.last_answer.length === 6){
            this.last_answer = []
            this.last_answer.push(option_value)
            this.last_answer.push((new Date().getMinutes() * 60) + new Date().getSeconds() )
          }
        }


      },
      Next(){
        this.Quiz_serial++ ;
        this.Compare();
      },
      Pass(){
        // this.Compare();
        document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('bg-RevsionColor')
        setTimeout(() => {
          if(this.Quiz_serial < this.Quiz_data.length - 1){
            this.Quiz_serial++
          }
          document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('bg-CurrentColor')

        }, 500);
      },
      Previous(){
        this.Quiz_serial-- ;
        this.Compare();
      },
      Finish_Quiz(){
        localStorage.setItem(`Answered_${this.$route.params.slug}`, JSON.stringify(this.Answered));
        localStorage.setItem(`Quiz_data_${this.$route.params.slug}`, JSON.stringify(this.Quiz_data));
        this.$router.push({path:`/Result/${this.$route.params.slug}`})
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
            if(this.Minute === 0 && this.Remseconds === 1){
              this.SendData();
              this.status_code = '';
            }
          }
        },
        immediate: true,
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
.Pagination_label{
  cursor: pointer;
}
.ImgManger{
  max-height: 400px;
  width: 100%;
}
.box{
  width: 20px;
  height: 20px;
}
.Responsive4{
  display: grid;
  grid-template-columns: auto auto ;
  align-items: flex-start;
  grid-column-gap: 25px;
}
.Responsive{
  display: grid;
  grid-template-columns: auto  ;
}
</style>
