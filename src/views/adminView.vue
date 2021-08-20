<template>
  <div class="wrap">
    <!-- <div v-if="dbDownloaded == false">
      <div class="loader"></div>
    </div> -->
    <div class="chart" v-if="dbDownloaded == true && isAdmin == true">
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
      <div class="login">
        <h1 class="title">Admin key</h1>
        <div class="form">
          <input class="txt" type="text" placeholder="key" v-model="key" />
          <button @click="login">Submit</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AdminComp from "../components/adminComp.vue";
import firebase from "firebase/app";
import firestore from "firebase/firestore";
import Swal from "sweetalert2";
export default {
  name: "adminView",
  components: { AdminComp },
  data() {
    return {
      db: [],
      dbDownloaded: false,
      isAdmin: false,
      key: "",
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
    login() {
      let key = "";
      firebase
        .firestore()
        .collection("key")
        .doc("admin")
        .get()
        .then((doc) => {
          key = doc.data();
          if (this.key == key.key) {
            this.isAdmin = true;
            Swal.fire({
              title: "Bienvenido Admin",
              text: "",
              icon: "success",
              confirmButtonText: "Cerrar",
              position: "top-right",
              toast: true,
              timer: 1500,
            });
          } else {
            Swal.fire({
              title: "Error",
              text: "Key incorrecta",
              icon: "error",
              confirmButtonText: "Cerrar",
              position: "top-right",
              toast: true,
              timer: 1500,
            });
          }
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
.login {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: whitesmoke;
  width: 800px;
  height: 800px;
}
.title {
  margin-top: 150px;
}
.form {
  display: flex;
  flex-direction: column;
  margin-top: 50px;
}
.txt {
  margin-top: 20px;
  padding: 0px 10px;
  outline: none;
  border: none;
  width: 300px;
  height: 50px;
  border-radius: 5px;
  font-size: 18px;
}
button {
  font-size: 18px;
  margin-top: 50px;
  height: 50px;
  border-radius: 25px;
  border: none;
  outline: none;
  background-color: #14b9a9;
  cursor: pointer;
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
