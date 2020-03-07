<template>
<div>
  <div v-if="!status || status ===0">
  <h1>{{errorStatement}}</h1>
  <h3 class="warning">{{head}}</h3>
  <h5 class="danger">check your network connection and click the button below!!!</h5>
  <v-progress-linear
      color="red lighten-2"
      buffer-value="0"
      stream
    ></v-progress-linear> <br/>
    <router-link to="/">
          <v-btn @click="refresh()" rounded color="white--text primary">Login Again</v-btn>
    </router-link>

  </div>
  <Error v-if="status===401"/>
  <v-card
    v-if="status===200"
    class="mx-auto profile"
    max-width="600"
  >
    <!-- <v-img
      src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
      height="200px"
    ></v-img> -->

    <Subscriptions />
    <v-card-title>
    </v-card-title>
    <v-card-subtitle>
      <router-link to="/" class="router" >
      <v-btn @click="exiting()"  color="error">LOGOUT NOW</v-btn>
      </router-link>
     <h2> Hello, {{student.firstName}} welcome to OUC_ZIM students fund</h2>
      <a class="danger" v-if="student.payment===0">Please pay your subscriptions due, to activate your account!!!</a>
      <h5 v-if="student.payment===0"><a>Only active member can view more details about the ZIM_OUC fund, your name does not exist in OUC_ZIM fund system</a></h5>
      <h4 v-if="student.payment > 0" class="pt-2">Your total Subscriptions Balance is  ¥{{student.payment}}</h4>
      <!-- <h3 v-if="student.payment > 0" class="pt-2">Current total fund balance is ¥{{student.payment}}</h3> -->
      <h4 v-if="student.payment > 0" class="pt-2"><a>Month(s) Subscribed: </a>{{student.payment/30}}<a v-if="student.payment===1"> month</a>  <a v-else> months</a> only</h4>


      <h3 class="pt-2"><a>Contact Secretary for more Details</a></h3>
      
    </v-card-subtitle>

    <v-card-actions>

    <!-- <router-link to="/">
      <v-btn
        color="primary darken-2 white--text"
        text
      >
        Sign Out
      </v-btn>
    </router-link> -->

      <v-spacer></v-spacer>

      <v-btn
        icon
        @click="show = !show"
      >
        <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
      </v-btn>
    </v-card-actions>

    <v-expand-transition>
      <div v-show="show">
        <v-divider></v-divider>
     
        <v-card-text>
           <!-- Balance left ¥{{time}} coming soon<br>
           You've paid for ¥{{time}} coming soon<br>  
           Expenses deducted ¥{{time}} coming soon<br>  -->
            <Subscriptions /> 
        </v-card-text>
      </div>
    </v-expand-transition>
  </v-card>
</div>
</template>

<script>
import Subscriptions from "./Subscriptions";
import Error from "../Error";
 import {mapState,mapActions} from 'vuex';
  export default {
    mounted(){
   const student = JSON.parse(localStorage.getItem('student'));
   this.$store.state.student = student;
   if(student){
     this.$store.state.status = 200;
   }
   setTimeout(()=>{
     this.$store.state.errorStatement = '';
     this.$store.state.head='OOPS! SOMETHING WENT WRONG.'
   },5000);
    },
      components: { 
    Subscriptions,
    Error
     },
       computed:{
    ...mapState([
      'status',
      'student',
      'token',
      'userId',
      'wrongDetails',
      'notExist',
      'nameError',
      'studentNumber',
      'errorStatement',
      'head'
    ])
  },
    data: () => ({
      show: false,
    }),
    methods:{
      exiting(){
      localStorage.removeItem('studentNum');
      this.$store.state.token = null;
      this.$store.state.isAuth = false;
      this.$store.state.studentNumber = 'BK';
      localStorage.clear();
      // localStorage.removeItem('token');
      // localStorage.removeItem('expiryDate');
      // localStorage.removeItem('userId');
      // localStorage.removeItem('studentNum');
      this.$store.state.student = null;
      console.log(`exiting token now ${this.$store.state.token}`);
      
    },
    refresh(){
             this.$store.state.status = null;
         }
    }
  }
</script>

<style scoped>
    .profile{
        top:0px !important;
    }
    .danger{
      color: orangered;
    }
</style>