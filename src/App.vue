<template>
  <div id="nav">
    <!-- <router-link to="/">Home</router-link>  -->
    <!-- <router-link to="/about">About</router-link> -->
    <Information />
    <div class="view" v-if="false">
      <CalendarComponent @dateSelected="newDate" />
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
    };
  },
  methods: {
    newDate(e) {
      this.fecha = e;
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
