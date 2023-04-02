<template>
  <div v-show="isInit" class="spinner-border spinner-border-sm" role="status"></div>
  <div v-if="!isInit">
    <div class="map-wrapper">
      <l-map ref="map" :zoom="zoom" :center="center">
        <l-tile-layer ref="layer" :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker v-for="marker in markers" :key="marker" :lat-lng="marker.latLng"></l-marker>
      </l-map>
    </div>
    <div class="search-field">
      <div>
        <input class="control" type="text" v-model="searchValue"/>
        <button class="control" @click="searchLocation()">Meklēt</button>
        <p v-show="showAlert" class="red-alert">Ievadiet atslēgvārdu!</p>
      </div>
      <div>
        <ul v-if="!isLoading" class="select-list">
          <li v-for="item in searchLocations" :key="item" class="record" @click="setLocation(item)">{{ item.std }}</li>
          <li v-if="!searchLocations.length">Nav rezultātu</li>
        </ul>
        <div v-show="isLoading" class="spinner-border spinner-border-sm" role="status"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";
import L from "leaflet";

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
  },

  data() {
    return {
      zoom: 7,
      center: [56.941307, 24.565996],
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      markers: [],
      defaultLocations: [],
      searchLocations: [],
      searchValue: "",
      isLoading: false,
      isInit: true,
      showAlert: false
    };
  },

  async beforeCreate() {
    await fetch("location/GetNeswLocations")
      .then(async response => { this.defaultLocations = await response.json();});
    
    this.defaultLocations.forEach(item => this.markers.push({latLng: L.latLng(item.dd_N, item.dd_E)}));
    this.isInit = false;
  },

  methods: {
    setLocation(item) {
      this.markers = [];
      this.markers.push({latLng: L.latLng(item.dd_N, item.dd_E), title: item.std});
    },

    async searchLocation() {
      if(this.searchValue.trim() === ""){
        return this.showAlert = true;
      }

      this.showAlert = false;
      this.isLoading = true;
      
      await fetch("location/SearchLocations?searchValue=" + this.searchValue)
        .then(async response => { this.searchLocations = await response.json();});
      this.isLoading = false;
    },
  }
};
</script>

<style>
@import "../styles/Locations.css";
</style>