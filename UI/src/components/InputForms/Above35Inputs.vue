<template>
  <MDBNavbar class="navbar-container" container>
    <router-link to="/" style="font-size: x-large; color:black;padding-left:50px">
      J M A C
    </router-link>
  </MDBNavbar>

<section class="vh-100 gradient-custom">
<div class="container py-5 h-100">
  <div class="row d-flex justify-content-center align-items-center h-100">
    <h2 class="fw-bold mb-2 text-uppercase">Probability Test for CVD</h2>
    <hr class="solid">
    <h5 class="fw-regular mb-2">Based on the patient's preliminary information, please enter the required additional information.</h5>
    <div class="float-container">
      <div class="float-child">
        <div class="col-12 col-md-8 col-lg-6 col-xl-12">
          <div class="form-floating mb-3">
            <input type="chol" class="form-control" id="cholFloatingInput" placeholder="e.g. 21" v-model="AdditionalInfo.chol">
            <label for="floatingInput">Cholesterol Level in mg/dl</label>
          </div>
          <div class="form-floating mb-3">
            <input type="fbs" class="form-control" id="fbsFloatingInput" placeholder="Male or Female" v-model="AdditionalInfo.fbs">
            <label for="floatingInput">Fasting Blood Sugar in mg/dl</label>
          </div>
          <div class="form-floating mb-3">
            <input type="restcg" class="form-control" id="restecgFloatingInput" placeholder="e.g. 145" v-model="AdditionalInfo.restecg">
            <label for="floatingInput">Resting Electrocardiographic Results</label>
          </div>
        </div>        
      </div>
      <div class="float-child">
        <div class="col-12 col-md-8 col-lg-6 col-xl-12">
          <div class="form-floating mb-3">
            <input type="thalach" class="form-control" id="thalachFloatingInput" placeholder="Yes or No" v-model="AdditionalInfo.thalach">
            <label for="floatingInput">Maximum Heart Rate</label>
          </div>
          <div class="form-floating mb-3">
            <input type="thal" class="form-control" id="thalfloatingInput" placeholder="e.g. 2" v-model="AdditionalInfo.thal">
            <label for="floatingInput">Thalassemia Type</label>
          </div>
          <button @click="handleSubmit" class="btn btn-lg px-5" type="submit">Submit</button>
          <p v-if="loading == true" class="loading-msg">Processing data. Please wait...</p>
        </div>
      </div>
    </div>
  </div>
</div>
</section> 
</template>


<script>
  //import { ref } from "vue";
  import { MDBNavbar } from 'mdb-vue-ui-kit';
  import axios from 'axios';

  export default {
    components: {
      MDBNavbar
    },
    data() {
      return {
        loading: false,
        responseData: null,
        PrelimInfo: {
          age: '',
          gender: '',
          trestbps: '',
          has_history: '',
          cp: ''
        },
        AdditionalInfo: {
          chol: '',
          fbs: '',
          restecg: '',
          thalach: '',
          thal: ''
        }
      };
    },
    methods: {
      async handleSubmit() {
        this.loading = true;
        try {
          const url = 'http://127.0.0.1:5000/Above35'; // Update the API endpoint here
          const response = await axios.post(url, {
            age: this.PrelimInfo.age,
            gender: this.PrelimInfo.gender,
            trestbps: this.PrelimInfo.trestbps,
            has_history: this.PrelimInfo.has_history,
            cp: this.PrelimInfo.cp,
            chol: this.AdditionalInfo.chol,
            fbs: this.AdditionalInfo.fbs,
            restecg: this.AdditionalInfo.restecg,
            thalach: this.AdditionalInfo.thalach,
            thal: this.AdditionalInfo.thal
          });

          // Note: No need for response.json() since axios already parses JSON

          if (response.status === 200) {
            console.log('Request successful:', response.data);
            this.responseData = response.data;
            localStorage.setItem('responseData', JSON.stringify(this.responseData))
            localStorage.setItem('AdditionalInfo', JSON.stringify(this.AdditionalInfo))
            //console.log(this.responseData.above_svm_probability);
          } else {
            console.error('Request failed:', response.data);
            alert('Request failed:' + response.data.message);
          }
        } catch (error) {
          console.error('Request failed:', error.message);
        }
        if (this.responseData.final_svm_probability >= 45){ //Redirect to the result page depending on the final outcome
          this.$router.push("/MainResult1");
        }
        else{
          this.$router.push("/MainResult0");
        }
      },
    },
    mounted() {
      this.PrelimInfo = JSON.parse(localStorage.getItem('PrelimInfo'))
    }
  };
</script>


<style scoped>
.vh-100 {
  height: 80vh !important;
}
.float-container {
    padding: 20px;
}
.float-child {
    width: 50%;
    float: left;
    padding: 20px;
}  
.navbar-container {
  height: 15vh;
  background: linear-gradient(to bottom, #8a8a8a,#b8b8b8,#d3d3d3,#ececec,#ffffff);
  font-weight:bolder;
  font-family:system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  box-shadow: none;
}
.gradient-custom {
  background: color(srgb rgb(255, 255, 255) rgb(255, 255, 255) rgb(255, 255, 255))
}
.loading-msg {
  font-style: italic;
  padding-top: 25px;
}
.btn {
  border-radius: 50px;
  background-color: #ffca7b;
  font-weight: bolder;
  margin-top: .5rem;
}
hr.solid {
  border-top: 3px solid #000000;
}
</style>