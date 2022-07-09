<template >
 <b-container class="my-4" >
  <div v-if="status_code === 'success' && this.Quiz_data.length > 0 ">
     <div class="d-flex justify-content-end">
        <p class="">السؤال رقم <span class="OrangeColor">{{Quiz_serial+1}}</span> من <span class="GreenColor">{{Quiz_data.length}}</span> سؤال</p>
      </div>
      <p class="text-center DarkBlueSecColor">{{Quiz_duration}}</p>
      <div class="d-flex w-100 mt-5 mb-4">
        <div class="" v-for="index in Quiz_data.length" :key="index">
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
                :class="selected === option.value ? 'selected rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options' : 'rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options'"
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
            <b-button type="button" size="lg" variant="outline-danger" class="p rounded f-14 mx-3"  v-on:click="Pass">مؤجل</b-button>
            <b-button type="button" size="lg" class="p rounded f-14 btn-secondary" v-if="Quiz_serial > 0 " v-on:click="Previous">السابق</b-button>
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
</template>

<script>
// import config from "@/config";
import Loading from "@/components/local/Loading";
  export default {
    components:{
      Loading,
    },
    data() {
      return {
        Quiz_data: [],
        Answered:[],
        Answered_obj : {},
        selected: '',
        status_code: '',
        Quiz_serial:0,
        Quiz_duration:0,
        Minute:0,
        Seconds:0,
        Remseconds:0,
      }
    },
    mounted() {
      this.fetchData();
    },
    methods: {
      fetchData() {
        this.SendData();
        var myHeaders = new Headers();
        myHeaders.append("Authorization", "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9sb2NhbGhvc3RcL3dvcmRwcmVzcyIsImlhdCI6MTY1NzE5NTE5MywibmJmIjoxNjU3MTk1MTkzLCJleHAiOjE2NTc3OTk5OTMsImRhdGEiOnsidXNlciI6eyJpZCI6IjEifX19.1aF7JCRBx4YrgtA622JyNsUvKk1DMXhJPsI8pmuKTRI");
        myHeaders.append("Content-Type", "application/json");

        var raw = JSON.stringify({"id":"95"});

        var requestOptions = {
          method: 'POST',
          headers: myHeaders,
          body: raw,
          redirect: 'follow'
        };

        fetch("http://localhost/wordpress/wp-json/learnpress/v1/quiz/start", requestOptions)
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
      SendData() {
        var myHeaders = new Headers();
        myHeaders.append("Authorization", "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9sb2NhbGhvc3RcL3dvcmRwcmVzcyIsImlhdCI6MTY1NzE5NTE5MywibmJmIjoxNjU3MTk1MTkzLCJleHAiOjE2NTc3OTk5OTMsImRhdGEiOnsidXNlciI6eyJpZCI6IjEifX19.1aF7JCRBx4YrgtA622JyNsUvKk1DMXhJPsI8pmuKTRI");
        myHeaders.append("Content-Type", "application/json");

        if(this.Answered.length > 0){
          for (var i=0; i<this.Answered.length; i++){
            this.Answered_obj[this.Answered[i].id] = {answered: `${this.Answered[i].answer}`} ;
          }
          console.log("Answered_obj",this.Answered_obj)
        }

        var raw = JSON.stringify({"id":"95", "answered" : this.Answered_obj});

        var requestOptions = {
          method: 'POST',
          headers: myHeaders,
          body: raw,
          redirect: 'follow'
        };

        fetch("http://localhost/wordpress/wp-json/learnpress/v1/quiz/finish", requestOptions)
          .then(response => response.text())
          .then(res => {
            console.log('finisf',JSON.parse(res))
            this.status_code = JSON.parse(res).status;
          })
          .catch(error => console.log('error', error));

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
      },
      Save(id, option_value, my_Quiz_serial){
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

      },
      Next(){
        this.Quiz_serial++ ;
        this.Compare();
      },
      Pass(){
        if(this.Quiz_serial < this.Quiz_data.length - 1){
          this.Quiz_serial++
        }
        // this.Compare();
        document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('bg-RevsionColor')

      },
      Previous(){
        this.Quiz_serial-- ;
        this.Compare();
      },
      Finish_Quiz(){
        this.status_code === ''
        this.SendData();
        if(this.status_code === 'success'){
          this.$router.push({path:'/Result'})
        }

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
            if(this.Remseconds === 1){
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
  justify-content: center;
  grid-column-gap: 25px;
}
.Responsive{
  display: grid;
  grid-template-columns: auto  ;
}
.options{
  min-height: 100px;
}
</style>
