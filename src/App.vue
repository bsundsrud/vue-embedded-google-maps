<script setup>
import { ref } from "vue";
import MapChooser from "./components/MapChooser.vue";

const lat = ref(41.3560317);
const lng = ref(-71.8843789);
const edit = ref(false);

const addressEdit = ref("Park Rapids, MN 56470");
const address = ref("Park Rapids, MN 56470");

const changed = (newLat, newLng) => {
  lat.value = newLat;
  lng.value = newLng;
};

function clear() {
  lat.value = undefined;
  lng.value = undefined;
}

function changeAddress() {
  address.value = addressEdit.value;
  console.log(address.value, addressEdit.value)
}

function toggleEdit() {
  edit.value = !edit.value;
}
</script>

<template>
  <div class="map-container">
    <div class="coords">
      <a @click="toggleEdit">{{ edit ? "Save" : "Edit" }}</a>
      <span>Coordinates: {{ lat }}, {{ lng }}</span>
      <a @click="clear">Clear</a>
      <input type="text" v-model="addressEdit">
      <a @click="changeAddress">Update Address</a>
    </div>
    <MapChooser @change="changed" :edit="edit" :lat="lat" :lng="lng" :address="address" />
  </div>
</template>

<style scoped>

  .map-container {
    width: 100%;
    height: 95%;
    
  }

  .map-container a {
    color: #00f;
    text-decoration: underline;
    cursor: pointer;
    margin-left: 2px;
    margin-right: 2px;
  }
  .coords {
    padding: 10px;
    margin: 0;
  }
</style>
