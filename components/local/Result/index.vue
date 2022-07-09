<template>
  <div>
    <b-container class="my-4" >
      <div v-if="status_code === 'success' && this.Result.length > 0 ">
       <div class="text-center">
         <p>question_answered : {{Result[0].question_answered}}</p>
          <p>question_correct : {{Result[0].question_correct}}</p>
          <p>question_wrong : {{Result[0].question_wrong}}</p>
          <p>question_empty : {{Result[0].question_empty}}</p>
          <p>question_count : {{Result[0].question_count}}</p>
          <p>Total mark : {{Result[0].mark}}</p>
          <p>your mark : {{Result[0].result}}</p>
       </div>

      </div>
      <div v-else class="d-flex justify-content-center align-items-center spinner_loading">
        <Loading/>
      </div>
    </b-container>

  </div>
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
        Answered:[],
        Result:[],
        Answered_obj : {},
        status_code: '',
      }
    },
    mounted() {
      this.Finish_Quiz();
    },
    methods: {
      SendData() {
        var myHeaders = new Headers();
        myHeaders.append("Authorization", "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC9sb2NhbGhvc3RcL3dvcmRwcmVzcyIsImlhdCI6MTY1NzE5NTE5MywibmJmIjoxNjU3MTk1MTkzLCJleHAiOjE2NTc3OTk5OTMsImRhdGEiOnsidXNlciI6eyJpZCI6IjEifX19.1aF7JCRBx4YrgtA622JyNsUvKk1DMXhJPsI8pmuKTRI");
        myHeaders.append("Content-Type", "application/json");

        if(this.Answered.length > 0){
          for (var i=0; i<this.Answered.length; i++){
            this.Answered_obj[this.Answered[i].id] = {answered: `${this.Answered[i].answer}`} ;
          }
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
            this.Result = [JSON.parse(res).results];

          })
          .catch(error => console.log('error', error));

      },
      Finish_Quiz(){
        console.log('localStorage.',JSON.parse(localStorage.getItem("Answered")))
        this.Answered = JSON.parse(localStorage.getItem("Answered"))
        console.log("this.Answered",this.Answered)
        this.SendData();
      },

    },

  }
</script>

