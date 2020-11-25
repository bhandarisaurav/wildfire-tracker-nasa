<template>
  <div class="map">
    <div class="title">NASA's Wildfire Tracker</div>
    <l-map :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker :lat-lng="marker"></l-marker>
    </l-map>
  </div>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker } from "vue2-leaflet";

export default {
  name: "Map",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    // LPopup,
    // LTooltip,
  },
  data() {
    return {
      zoom: 5,
      center: latLng(37.09024, -95.712891),
      marker: latLng(37.09024, -95.712891),
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
    };
  },
  methods: {},
};
</script>

<style scoped>
.title {
  margin-bottom: 13px;
  height: auto;
  overflow: auto;
  font-family: roboto;
  font-weight: bold;
  font-size: 30px;
  color: darkcyan;
}

.map {
  margin: 0 auto;
  height: 565px;
  width: 98%;
}
</style>