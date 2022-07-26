<template>
  <div>
      <AppNav/>
    <b-container class="my-4" >
      <div v-if="status_code === 'success' && this.Result.length > 0  ">


       <div class="d-flex justify-content-between">
          <p class="DarkBlueSecColor">مجموع درجاتك <span>{{Result[0].user_mark}}</span>من أصل <span>{{Result[0].mark}}</span></p>
          <p class="">السؤال رقم <span class="OrangeColor">{{Quiz_serial+1}}</span> من <span class="GreenColor">{{Quiz_data.length}}</span> سؤال</p>
        </div>

        <p v-on:click="table_mode = !table_mode" class="cursor BlueColor f-14 text-center">{{table_mode === true ? 'مزيد من التفاصيل...' : 'القليل من التفاصيل...'}}</p>
        <b-table :class="table_mode === true ? 'd-none': 'd-inline-table '" striped hover :items="Result_table"></b-table>


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
          <span class="f-14">صح</span>
          <span class="box bg-GreenColor mx-2"></span>
          <span class="f-14">خطأ</span>
          <span class="box bg-RedColor  mx-2"></span>
        </div>

        <p class="DarkBlueSecColor f-14"> {{Quiz_data[Quiz_serial].title}}</p>
        <p v-html="Quiz_data[Quiz_serial].content"></p>

        <b-row align-h="center"  class="flex-wrap-reverse  justify-content-center align-items-end">
          <b-col :class="Quiz_data[Quiz_serial].paragraph || Quiz_data[Quiz_serial].thumbnail ? 'col-11 col-lg-6 col-md-8 col-sm-6 px-3 my-4' : 'col-11 px-3 my-4'">
            <div :class="Quiz_data[Quiz_serial].paragraph || Quiz_data[Quiz_serial].thumbnail ? 'Responsive' : 'Responsive4' ">
              <div v-for="option in Quiz_data[Quiz_serial].options" :key="option.uid">
                  <label
                  class="rounded bg-DarkGrayColor px-4 py-3 border_0 mb-3 w-100 options"
                  :id="option.value"
                  >
                  {{option.title}}
                  </label>
                </div>
            </div>


            <div class="mt-3 d-none">Selected: <strong>{{ selected }}</strong></div>
            <div class="w-100 d-flex justify-content-center">
              <b-button type="button" size="lg" class="p rounded border-0 f-14 btn btn-secondary mx-3"  v-if="Quiz_serial !== Quiz_data.length-1 " v-on:click="Next">التالي</b-button>
              <b-button type="button" size="lg" class="p rounded f-14" variant="outline-danger" v-if="Quiz_serial > 0 " v-on:click="Previous">السابق</b-button>
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
import AppNav from '@/components/Global/AppNav';
import Loading from "@/components/local/Loading";

  export default {
    components:{
      Loading,
      AppNav,
    },
    data() {
      return {
        Answered:[],
        Result:[],
        Quiz_data:[],
        Answered_obj : {},
        selected: '',
        status_code: '',
        Quiz_serial:0,
        Result_table:[],
        table_mode: true,
      }
    },
    mounted() {
      this.Finish_Quiz();
      setTimeout(() => {
        this.Compare();
        this.PaginationCompare();
        this.Result_table_fun();
      }, 3000);
    },
    methods: {
      SendData() {
        var myHeaders = new Headers();
        myHeaders.append("Authorization", `Bearer${config.token}`);
        myHeaders.append("Content-Type", "application/json");

        if(this.Answered.length > 0){
          for (var i=0; i<this.Answered.length; i++){
            this.Answered_obj[this.Answered[i].id] = {answered: `${this.Answered[i].answer}`} ;
          }
        }

        var raw = JSON.stringify({"id":this.$route.params.slug, "answered" : this.Answered_obj});

        var requestOptions = {
          method: 'POST',
          headers: myHeaders,
          body: raw,
          redirect: 'follow'
        };

        fetch(config.apiUrl+"wp-json/learnpress/v1/quiz/finish", requestOptions)
          .then(response => response.text())
          .then(res => {
            console.log('finisf',JSON.parse(res))
            this.status_code = JSON.parse(res).status;
            this.Result = [JSON.parse(res).results];
          })
          .catch(error => console.log('error', error));


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
        // console.log('localStorage.',JSON.parse(localStorage.getItem("Answered_95")))
        // console.log('localStorage.',JSON.parse(localStorage.getItem("Quiz_data_95")))
        this.Answered = JSON.parse(localStorage.getItem(`Answered_${this.$route.params.slug}`));
        this.Quiz_data = JSON.parse(localStorage.getItem(`Quiz_data_${this.$route.params.slug}`));
        this.SendData();


      },
      Pagination(index){
        this.Quiz_serial = index -1;
        this.Compare();
      },
      PaginationCompare(){
        let arr_Pagination_label = document.querySelectorAll('.Pagination_label');
        arr_Pagination_label.forEach(element => {
          if(this.Result[0].answered[element.id].correct === true){
            element.classList.add('bg-GreenColor')
          }else{
            element.classList.add('bg-RedColor')
          }

        });

      },
      Compare(){
        setTimeout(() => {
           let arr_Pagination_label = document.querySelectorAll('.Pagination_label');
            arr_Pagination_label.forEach(element => {
              element.classList.remove('current_border')
            });
          document.getElementById(this.Quiz_data[this.Quiz_serial].id).classList.add('current_border')

          let options = document.querySelectorAll('.options');
          options.forEach(element => {

            if(this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].correct === true){
              console.log("ddd",this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id])
              if(element.id === this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].answered ||
                element.id === this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].answered.answered){
                element.classList.add('bg-OpacityGreenColor');
              }

            }

            if(this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].correct === false){

              this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].options.forEach(ele => {
                if(ele.is_true === 'yes'){
                  document.getElementById(ele.value).classList.add('bg-OpacityGreenColor');
                }
                if(this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].answered === ''){
                  null
                }else{
                  document.getElementById(this.Result[0].answered[this.Quiz_data[this.Quiz_serial].id].answered).classList.add('bg-OpacityRedColor');
                }


              });
            }

          });

        }, 300);
      },
      Result_table_fun(){
        if(this.Result[0].tags !== undefined){
           for(const category in this.Result[0].tags){
            this.Result_table.push({
              'اسم القسم': category,
              'إجمالي الأسئلة': this.Result[0].tags[category].total_questions,
              'عدد الإجابات الصحيحة': this.Result[0].tags[category].correct_answers,
              'النسبة' : this.Result[0].tags[category].score
            })
          }
        }

      },
    },

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
  color: #fff;
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
