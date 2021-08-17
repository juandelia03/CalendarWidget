<template>
  <div id="nav">
    <!-- <router-link to="/">Home</router-link>  -->
    <!-- <router-link to="/about">About</router-link> -->
    <Information @infoSubmited="personalInfo" v-if="steps.step1" />
    <div class="view" v-if="steps.step2">
      <CalendarComponent
        @dateSelected="newDate"
        @backToForm="
          () => {
            steps.step1 = true;
            steps.step2 = false;
          }
        "
        @nextToEnd="end"
      />
    </div>
    <FinalScreen
      v-if="steps.step3"
      :client="clientInformation"
      :date="fecha"
      @cancel="
        () => {
          steps.step1 = true;
          steps.step2 = false;
          steps.step3 = false;
        }
      "
      @submit="pushToDb"
    />
  </div>
  <router-view />
</template>

<script>
/* eslint-disable */
import CalendarComponent from "./components/Calendar.vue";
import Information from "./components/Information.vue";
import FinalScreen from "./components/FinalScreen.vue";
import firebase from "firebase/app";
import firestore from "firebase/firestore";
import Swal from "sweetalert2";
export default {
  name: "App",
  components: {
    CalendarComponent,
    Information,
    FinalScreen,
  },
  data() {
    return {
      db: [],
      fecha: new Date(),
      clientInformation: {
        name: "Juan",
        last: "D'elia",
        cell: "+5491138457068",
        email: "juan@gmail.com",
        message: "",
      },
      steps: {
        step1: true,
        step2: false,
        step3: false,
      },
    };
  },
  methods: {
    newDate(e) {
      this.fecha = e;
    },
    personalInfo(e) {
      this.clientInformation = e;
      this.steps.step1 = false;
      this.steps.step2 = true;
    },
    pushToDb() {
      let each = this.fecha.toString().split(" ");
      each = [each[0], each[1], each[2], each[3]];
      each = each.join("-");
      console.log(this.clientInformation, this.fecha, each);
      firebase
        .firestore()
        .collection("apointments")
        .add({
          ClientsInfo: this.clientInformation,
          date: this.fecha,
          parsedDate: each,
        })
        .then(() => {
          Swal.fire({
            title: "Done!",
            text: "Your appointment was booked",
            icon: "success",
            confirmButtonText: "Close",
          });
          setTimeout(() => {
            location.reload();
          }, 1000);
        })
        .catch(() => {
          Swal.fire({
            title: "Error!",
            text: "An error ocurred",
            icon: "error",
            confirmButtonText: "Close",
          });
          setTimeout(() => {
            location.reload();
          }, 1000);
        });
    },
    end() {
      if (this.fecha >= new Date()) {
        this.steps.step2 = false;
        this.steps.step3 = true;
      } else {
        Swal.fire({
          title: "Error!",
          text: "You can not pick a date in the past",
          icon: "error",
          confirmButtonText: "Close",
        });
      }
    },
  },
  created() {
    firebase
      .firestore()
      .collection("apointments")
      .orderBy("date")
      .get()
      .then((query) => {
        query.forEach((doc) => {
          this.db.push(doc.data());
        });
      });
    // .then(() => {
    //   console.log(this.db[0].date);
    // });
  },
};
</script>
<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");
* {
  font-family: "Roboto", sans-serif;
  margin: 0;
  padding: 0;
}
.view {
  margin: 100px;
  display: flex;
  justify-content: center;
  min-height: 100vh;
  height: 100%;
}
</style>
