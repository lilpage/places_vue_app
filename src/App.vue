<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <!-- Index  -->
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }} : {{ place.address }}</h2>
      <!-- Show -->
      <button v-on:click="showPlace(place)">Show</button>
      <dialog id="place-details">
        <form method="dialog">
          <h1>Did you wanna see this bigger? {{ place.name }} : {{ place.address }}</h1>
          <button>Ok, thanks</button>
        </form>
      </dialog>
      <!-- Update -->
      <button v-on:click="editPlace(place)">Update</button>
      <dialog id="place-update">
        <form method="dialog">
          Name:<input type="text" v-model="placeName">
          Address:<input type="text" v-model="placeAddress">
          <button v-on:click="updatePlace(place)">Update</button>
          <button>Close</button>
        </form>
      </dialog>
      <!-- Destroy -->
        <button v-on:click="destroyPlace(place)">Destroy</button>
    </div>
    <!-- Create -->
    <h3>Add a New Spot:</h3>
    Name: <input type="text" v-model="placeName">
    Address: <input type="text" v-model="placeAddress">
    <button v-on:click="createPlace()">Add Spot</button>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
  </div>
</template>

<style>
body {
  color: goldenrod;
  font-family: sans-serif;
}
h3 {
  color: maroon;
}
</style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Logan Square Spots",
      places: [],
      placeName: "",
      placeAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlace();
  },
  methods: {
    indexPlace: function () {
      axios.get("/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    showPlace: function (place) {
      axios.get("/api/places/" + place.id).then((response) => {
        console.log(response.data);
        document.querySelector("#place-details").showModal();
      });
    },
    createPlace: function () {
      let params = {
        name: this.placeName,
        address: this.placeAddress,
      };
      axios.post("/api/places/", params).then((response) => {
        console.log(response);
        this.places.push(response.data);
      });
    },
    editPlace: function (place) {
      document.querySelector("#place-update").showModal();
      console.log(place);
    },
    updatePlace: function (place) {
      let params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios.delete("/api/places/" + place.id).then(() => {
        console.log(`This has destroyed place_id:${place.id}`);
        this.places.splice(this.places.indexOf(place), 1);
      });
    },
  },
};
</script>