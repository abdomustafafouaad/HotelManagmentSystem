<template>
  <v-container>
    <div v-if="restaurant || car" class="wrapper">
      <Restaurant v-on:hide="hide()" v-if="restaurant" />
      <Car v-on:hide="hide()" v-if="car" />
    </div>

    <v-row>
      <v-col v-for="item in Services" :key="item.name" cols="4">
        <v-card class="mx-auto" max-width="400">
          <v-img class="white--text align-end" height="200px" :src="item.image">
          </v-img>

          <v-card-subtitle class="pb-0">
            {{ item.serviceName }}
          </v-card-subtitle>

          <v-card-text class="text--primary">
            <div>{{ item.description }}</div>
          </v-card-text>

          <v-card-actions>
            <v-btn
              v-if="$store.state.user.type == 'user'"
              color="primary"
              style="color: green"
              @click="showModal(item.serviceName, item.fees, item.id)"
              text
            >
              Book
            </v-btn>
            <v-spacer></v-spacer>
            {{ item.fees }}$
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
import Restaurant from "./Restaurant";
import Car from "./CarRental";
import Axios from "axios";
export default {
  components: {
    Car,
    Restaurant,
  },
  created: function () {
    Axios.get("http://127.0.0.1:8000/api/services/")
      .then((response) => {
        this.Services = response.data.results;
      })
      .catch(function (error) {
        console.log(error);
      });
  },
  data() {
    return {
      car: false,
      restaurant: false,
      Services: [],
      user: this.$store.state.user,
    };
  },
  methods: {
    showModal: function (name, fee, id) {
      let random = Math.floor(Math.random() * this.user.fees.length);

      let book = {
        user: this.user.id,
        roomNumber: this.user.fees[random].room,
        bookingType: false,
        service: id,
      };
      console.log(book.room);
      Axios.post("http://127.0.0.1:8000/api/book/", book)
        .then((response) => console.log(response))
        .catch((error) => console.log(error));
      console.log(this.user);
      Axios.put(`http://127.0.0.1:8000/api/fee/${id}/`, {
        user: this.user.id,
        room: this.user.fees[random].room,
        totalFees: fee,
      });
      alert("Request submitted successfully");

      if (name == "Car rental") {
        this.car = true;
      } else if (name == "Restaurant reservation") {
        this.restaurant = true;
      } else {
      }
    },
    hide: function () {
      this.car = false;
      this.restaurant = false;
    },
  },
};
</script>
<style scoped>
.wrapper {
  position: absolute;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.4);
  z-index: 5;
  left: 0;
  top: 0;
  height: 100%;
}
</style>
