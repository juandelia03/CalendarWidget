<template>
  <div class="wrap">
    <div class="chart" v-if="dbDownloaded == true">
      <AdminComp
        v-for="reservation in db"
        :key="reservation.ClientsInfo.patente.toUpperCase().replace(/[^\w\s!?]/g, '')"
        :patente="reservation.ClientsInfo.patente.toUpperCase().replace(/[^\w\s!?]/g, '')"
        :name="reservation.ClientsInfo.name"
        :lastName="reservation.ClientsInfo.last"
        :mail="reservation.ClientsInfo.email"
        :cell="reservation.ClientsInfo.cell"
        :id="reservation.ClientsInfo.id.trim().replace(/[^0-9]/g, '')"
        :date="reservation.parsedDate"
        :time="reservation.hour"
        @dlt="dlt"
      />
    </div>
    <div v-else>
      <div class="loader"></div>
    </div>
  </div>
</template>

<script>
import AdminComp from "../components/adminComp.vue";
import firebase from "firebase/app";
import firestore from "firebase/firestore";
export default {
  name: "adminView",
  components: { AdminComp },
  data() {
    return {
      db: [],
      dbDownloaded: false,
    };
  },
  methods: {
    dlt(patente, id) {
      const i = this.db.findIndex(
        (x) => x.ClientsInfo.patente.toUpperCase().replace(/[^\w\s!?]/g, "") == patente
      );
      this.db.splice(i, 1);
      firebase.firestore().collection("appointments").doc(patente).delete();
      firebase
        .firestore()
        .collection("idCounter")
        .doc(id)
        .update({
          counter: firebase.firestore.FieldValue.increment(-1),
        });
    },
  },
  created() {
    firebase
      .firestore()
      .collection("appointments")
      .orderBy("date")
      .get()
      .then((query) => {
        query.forEach((doc) => {
          this.db.push(doc.data());
        });
      })
      .then(() => {
        this.dbDownloaded = true;
      });
  },
};
</script>

<style scoped>
.wrap {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.chart {
  display: flex;
  width: 90%;
  height: 90vh;
  border: darkgray 1px solid;
  flex-direction: column;
  overflow-y: scroll;
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none;
  background-color: whitesmoke;
  border-radius: 15px;
}
.chart::-webkit-scrollbar {
  display: none;
}
.loader {
  border: 16px solid #f3f3f3; /* Light grey */
  border-top: 16px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
