<template>
  <v-app class="grey lighten-4">
    <!-- <Navbar /> -->

    <v-content class="mx-4 mb-4">
      <router-view></router-view>
    </v-content>
    <!-- <Footer /> -->
  </v-app>
</template>

<script>
// import Navbar from "./components/Navbar";
// import Footer from "./components/Footer";
import {mapActions} from "vuex";
import openSocket from 'socket.io-client';
export default {
  components: { /*Navbar, Footer */},
  name: "App",
  data() {
    return {};
  },
  computed:{
    ...mapActions([
      'loadDetails',
      'loggingOutAuto'
    ])
  },
  mounted(){
    const student = JSON.parse(localStorage.getItem('student'));
    const adminToken = localStorage.getItem('adminToken');
    this.$store.state.adminToken = adminToken;
  //  this.$store.state.student = null;
   console.log(`student obj here ${student.firstName}`);
    const token = localStorage.getItem('token');
    const studentNum =localStorage.getItem('studentNum');
    console.log(studentNum);
    
    /* eslint-disable */
    console.log(`mountent token ${token}`);
  
    const expiryDate = localStorage.getItem('expiryDate');
    console.log(expiryDate);
   
    if (!token || !expiryDate) {
      return;
    }
     if (new Date(expiryDate) <= new Date()) {
    this.$store.state.token = null;
    this.$store.state.student = null;
    this.$store.state.studentNumber =  'BK';
    this.$store.state.students =null;
    this.$store.state.isAuth = false;
    localStorage.removeItem('token');
    localStorage.removeItem('expiryDate');
    localStorage.removeItem('userId');
    localStorage.removeItem('studentNum');
    localStorage.clear();
    return;
    }
    const userId = localStorage.getItem('userId');
    const remainingMilliseconds =
      new Date(expiryDate).getTime() - new Date().getTime();
      console.log(`time remaining ${remainingMilliseconds}`);
      this.setAutoLogout(remainingMilliseconds);
      this.$store.state.isAuth = true;
      this.$store.state.token = token;
      this.$store.state.userId = userId;
      this.$store.state.studentNumber = studentNum;
      this.loadDetails;
      console.log('Did mount here');
    
      console.log(this.$store.state.studentNumber);
      ////to here

      
      console.log(`token iri ${token}`);
      
  },
  methods:{
    setAutoLogout(milliseconds){
    setTimeout(() => {
    this.$store.state.studentNumber = 'BK';
    this.$store.state.token = null;
    this.$store.state.adminToken = null;
    this.$store.state.isAuth = false;
    this.$store.state.student = null;
    this.$store.state.students =null;
    localStorage.removeItem('token');
    localStorage.removeItem('expiryDate');
    localStorage.removeItem('userId');
    localStorage.removeItem('studentNum');
    localStorage.clear();
    }, milliseconds);
  }
  }
};
</script>
<style>

</style>
