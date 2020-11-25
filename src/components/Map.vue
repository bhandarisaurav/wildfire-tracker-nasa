<template>
  <div>
    <div class="map" v-if="!loading">
      <div class="title">NASA's Wildfire Tracker</div>
      <l-map :zoom="zoom" :center="center">
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker :lat-lng="marker">
          <l-popup>
            <div>
              I am a popup
              <p>
                Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque
                sed pretium nisl, ut sagittis sapien. Sed vel sollicitudin nisi.
                Donec finibus semper metus id malesuada.
              </p>
            </div>
          </l-popup>
        </l-marker>
      </l-map>
    </div>
    <Loader v-if="loading" />
  </div>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker, LPopup } from "vue2-leaflet";
import axios from "axios";
import { URL } from "./utils/API";
import Loader from "./Loader.vue";

export default {
  name: "Map",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    Loader,
    LPopup,
    // LTooltip,
  },
  data() {
    return {
      eventData: [],
      loading: true,
      zoom: 5,
      center: latLng(37.09024, -95.712891),
      marker: latLng(37.09024, -95.712891),
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
    };
  },
  methods: {
    async fetchData() {
      await axios
        .get(URL)
        .then((response) => {
          this.loading = false;
          this.eventData = response.data.events;
        })
        .catch((error) => {
          console.log("error", error);
        });
      this.filterEventsData();
    },

    filterEventsData() {
      const filteredEventsData = this.eventData.filter(
        (event) => event.categories[0].title === "Wildfires"
      );
      this.eventData = filteredEventsData;
    },
  },
  created() {
    this.fetchData();
  },
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