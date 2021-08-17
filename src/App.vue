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
      />
    </div>
  </div>
  <router-view />
</template>

<script>
/* eslint-disable */
import CalendarComponent from "./components/Calendar.vue";
import Information from "./components/Information.vue";
export default {
  name: "App",
  components: {
    CalendarComponent,
    Information,
  },
  data() {
    return {
      fecha: "",
      clientInformation: {},
      steps: {
        step1: false,
        step2: true,
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
