<template>
  <LoaderComponent v-if="loading" />
  <div class="map" v-if="!loading">
    <div class="title">NASA's Wildfire Tracker</div>
    <l-map :use-global-leaflet="false" ref="map" v-model:zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker v-for="(data, index) in formattedData" :key="index" :lat-lng="data.coordinates">
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
</template>

<script lang="ts">
import 'leaflet/dist/leaflet.css';
import { LMap, LTileLayer, LMarker, LPopup } from '@vue-leaflet/vue-leaflet';
import LoaderComponent from './LoaderComponent.vue';
import axios, {type AxiosResponse} from 'axios';
import { latLng, LatLng, type PointExpression } from 'leaflet';
interface EventData {
  id: string;
  title: string;
  geometries: {
    date: string;
    coordinates: number[];
  }[];
  categories: {
    title: string;
  }[];
}

interface FormattedData {
  id: string;
  title: string;
  date: string;
  coordinates: LatLng;
}

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LoaderComponent,
  },
  data() {
    return {
      eventData: [] as EventData[],
      formattedData: [] as FormattedData[],
      loading: true,
      zoom: 5,
      center: [37.09024, -95.712891] as PointExpression,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
    };
  },
  methods: {
    async fetchData() {
      const API_KEY = 'qJZA5ZM3xLg4nARkHqdsbZiy4qCJfXhSoRjQPwrd';
      const URL = `https://eonet.gsfc.nasa.gov/api/v2.1/events?api_key=${API_KEY}`;

      try {
        const response: AxiosResponse = await axios.get(URL);
        this.loading = false;
        this.eventData = response.data.events;
        this.filterEventsData();
      } catch (error) {
        console.error('Error:', error);
      }
    },

    filterEventsData() {
      const filteredEventsData = this.eventData.filter(
        (event) => event.categories[0].title === 'Wildfires'
      );
      this.eventData = filteredEventsData;
      this.formattedData = this.formatData();
    },

    formatData(): FormattedData[] {
      const formattedData = this.eventData.map((data) => {
        const obj: FormattedData = {
          id: data.id,
          title: data.title,
          date: new Date(data.geometries[0].date).toDateString(),
          coordinates: latLng(data.geometries[0].coordinates[1], data.geometries[0].coordinates[0]),
        };
        return obj;
      });
      return formattedData;
    },
  },
  mounted() {
    this.fetchData();
  },
};
</script>

<style scoped>
.map {
  height: 90vh;
  width: 96vw;
}

.title {
  margin-bottom: 13px;
  text-align: center;
  font-weight: bold;
  font-size: 30px;
  color: darkcyan;
}
</style>
