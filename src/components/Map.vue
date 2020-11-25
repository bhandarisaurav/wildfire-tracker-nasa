<template>
  <div>
    <div class="map" v-if="!loading">
      <div class="title">NASA's Wildfire Tracker</div>
      <l-map :zoom="zoom" :center="center">
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker
          v-for="(data, index) in formattedData"
          :key="index"
          :lat-lng="data.coordinates"
        >
          <l-popup>
            <div>
              <strong>{{ data.id }}</strong>
              <p>
                <strong>ON : {{ data.date }}</strong>
                <br />
                {{ data.title }}
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
      formattedData: [],
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
      this.formattedData = this.formatData();
    },

    formatData() {
      const formattedData = this.eventData.map((data) => {
        let obj = {};
        obj.id = data.id;
        obj.title = data.title;
        obj.date = new Date(data.geometries[0].date).toDateString();
        obj.coordinates = latLng(
          data.geometries[0].coordinates[1],
          data.geometries[0].coordinates[0]
        );
        return obj;
      });
      return formattedData;
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