<template>
<div>


  <div v-if="!status || status ===0 || !students">
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


    <v-row v-if="status===200 || students"
      align="center"
      justify="center"
    >
      <v-col class="text-center" cols="12">
        <!-- <Profile /> -->
      
  

 </v-col>
      <v-col class="text-center" cols="12">
        <Profile />
      </v-col>
    </v-row>
  
<div class="students" >
    <h1 class="subheading grey--text">Students</h1>

     <div>
    <!-- <v-breadcrumbs :items="items">
      <template v-slot:divider>
        <v-icon>mdi-forward</v-icon>
      </template>
    </v-breadcrumbs> -->
  </div>


 <div class="text-center">
    <v-btn rounded color="lighten-2 white--text primary mr-3 mx-0 mb-3" light
   
    >STUDENT REGISTER ONLY ACCESSED BY ADMINS</v-btn>
  </div>

     <v-simple-table fixed-header height="300px">
    <template v-slot:default>
      <thead>
        <tr>
          <th class="text-left">First Name</th>
          <!-- <th class="text-left">Last Name</th> -->
          <th class="text-left">Student_ID</th>
          <!-- <th class="text-left">Major</th> -->
          <th class="text-left">Total amt paid</th>
          <th class="text-left">Topup</th>

        </tr>
      </thead>
       <tbody>
          <tr v-for="student in students" :key="student.major">
            <td>{{ student.firstName }}</td>
            <!-- <td>{{student.lastName}}</td> -->
            <td>{{ student.studentID }}</td>
            <!-- <td>{{student.major}}</td> -->
            <td>{{ student.payment }}</td>
            <td>
   
    <v-expansion-panels>
      <v-expansion-panel
        
      >
        <v-expansion-panel-header>
          <!-- <v-icon class="mx-2" light center fab dark color="success">fa-caret-down</v-icon> -->
        </v-expansion-panel-header>
        <v-expansion-panel-content>
      <h5>{{student.studentID}}</h5>
      <v-row class="light--text" >
      <v-col cols="12" sm="3">
        <v-btn class="mx-2" fab dark color="indigo" @click="addThirty(student.id)">
            +30
          </v-btn>
      </v-col>
      <v-col cols="12" sm="3">
        <v-btn class="mx-2" fab dark color="indigo" @click="addBig(student.id)">
            +120
          </v-btn>
      </v-col>
      <v-col cols="12" sm="3">
        <v-btn class="mx-0" fab dark color="indigo" @click="subtractThirty(student.id)">
            -30
          </v-btn>
      </v-col>
      <v-col cols="12" sm="3">
        <v-btn class="mx-0" fab dark color="indigo" @click="subtractBig(student.id)">
            -120
          </v-btn>
      </v-col>
      <v-col cols="12" sm="3">
        <v-btn class="mx-0" fab dark color="indigo" @click="reset(student.id)">
            Reset
          </v-btn>
      </v-col>
    </v-row>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>
  
            </td>
          </tr>
        </tbody>
    </template>
  </v-simple-table>
  </div>
</div>
  
</template>

<script>
import Error from "../components/Error";
import openSocket from 'socket.io-client';
import {mapState, mapActions} from 'vuex';

export default {
  components: { 
    Error
     },
  data() {
    return {
      items: [
        {
          text: 'Dashboard',
          disabled: false,
          href: 'breadcrumbs_dashboard',
        },
        {
          text: 'Students',
          disabled: true,
          href: 'breadcrumbs_link_1',
        },
        {
          text: 'Financial Reports',
          disabled: false,
          href: 'breadcrumbs_link_2',
        },
      ],
       isMember:false,
      time: '',
      bk: "BK20170",
      post: "https://fundapi.herokuapp.com/v1/student/",
      student: 0,
      // students: this.$store.state.students,
      // payments: [],
      tabs: null,
      text: "Lorem ipsu",
      payment: {},
      firstName: "",
      lastName: "",
      major: "",
      pay: "",
      
       
    };
  },
  mounted() {
   const students = JSON.parse(localStorage.getItem('students'));
   if(students){
     this.$store.state.status = 200;
     console.log(students);
     
   } 
   const socket = openSocket('https://fundapi.herokuapp.com');
    socket.on('addition',data=>{
      if(data.action === 'payment'){
        this.load();
      }
    })
    console.log(`admin token ${adminToken}`);
       
    },
  computed:{
    ...mapState([
      'students',
      'adminToken',
      'secretId',
      'status',
      'students',
      'errorStatement',
      'head'
    ]),
    ...mapActions([
      'loadDetails'
    ])
    
  },
  methods: {
    refresh(){
             this.$store.state.status = null;
         },
    load(){
      fetch('https://fundapi.herokuapp.com/v1/payments/',{
            headers: {
              Authorization: 'Bearer ' + this.$store.state.adminToken
            }
          })
                  .then(response=>{
                    return response.json();
                    /* eslint-disable */
                  })
                  .then(data=> {
                    const studentsRecieved =[];
                    for(let key in data){
                      studentsRecieved.push(data[key]);
                    }
                    // this.students = studentsRecieved;
                    
                    
                    let students=[];
                    students = studentsRecieved;
                    this.$store.state.students = students;
                    // commit('SET_STUDENTS',students);
                    
                
                  }); 
    }
    ,
    reset(st) {
      this.$http
        .post(`https://fundapi.herokuapp.com/v1/reset/${st}`, {})
        .then(
          response => {
            /* eslint-disable */
            console.log(response);  // eslint-disable-next-line
          },
          error => {
            console.log(error);
          }
        );
    },
    addThirty(st) {
      this.$http
        .post(`https://fundapi.herokuapp.com/v1/payments/${st}`, {})
        .then(
          response => {
            /* eslint-disable */
            console.log(response);  // eslint-disable-next-line
          },
          error => {
            console.log(error);
          }
        );
    },
    addBig(st){
      this.$http
        .post(`https://fundapi.herokuapp.com/v1/payments100/${st}`, {})
        .then(
          response => {
            /* eslint-disable */
            console.log(response);
          },
          error => {
            console.log(error);
          }
        );

    },
    subtractThirty(st){
      this.$http
        .post(`https://fundapi.herokuapp.com/v1/subtract30/${st}`, {})
        .then(
          response => {
            /* eslint-disable */
            console.log(response);
          },
          error => {
            console.log(error);
          }
        );

    },
    subtractBig(st){
      this.$http
        .post(`https://fundapi.herokuapp.com/v1/subtract100/${st}`, {})
        .then(
          response => {
            /* eslint-disable */
            console.log(response);
          },
          error => {
            console.log(error);
          }
        );

    }
  
  }
};
</script>
 <style scoped>
 .students{
   margin:10px;
   padding:20px;
 }
  .parallax {
   min-height: 99vh !important;
}

/* .error{
  margin-top: 20vh;
} */
 </style>
