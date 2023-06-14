<template>
  <v-row justify="start" no-gutters>
    <ActivityDetail @closeDetail="{showDetail=false; refreshData()}" :detail="showDetail" :data="detailData"></ActivityDetail>
    <v-col class="list_col" no-gutters>
      <v-card>
        <v-list style="height:87vh; overflow-y:auto;">
          <medium
          v-if="activities.filter(item => {return filterSearch.some(key => {return item[key].toLowerCase().includes(searchItem)})}).length === 0"
          class="text-center d-block pt-4">
          Aucune activité n'a été trouvée</medium>
          <v-list-item v-for="(item, i) in activities.filter(item => {return filterSearch.some(key => {return item[key].toLowerCase().includes(searchItem)})})" :key="i" :value="item" @click="getDetail(item)" border="10">
            <template v-slot:prepend>
              <v-img v-if="!item.image" :width="300" aspect-ratio="16/9" cover src="https://cdn.vuetifyjs.com/images/parallax/material.jpg"></v-img>
              <v-img v-else-if="item.image!==''" :width="300" aspect-ratio="16/9" cover :src="item.image" class="image_fix"></v-img>
            </template>

            <template v-slot:append class="d-flex align-end mb-6">
              <div class="d-flex align-end mb-6">
                <v-icon class="me-1" icon="mdi-heart"></v-icon>
                <span class="subheading me-2">{{item.likes.length}}</span>
              </div>
            </template>

            <v-list-item-title>
              <div class="d-flex justify-center">
                <v-card width="100%" style="background-color: transparent;">
                  <v-card-title class="text-h6 text-md-h4 text-lg-h4">{{item.name}}</v-card-title>
                  <v-card-text>
                    <div class="text-md-h5 mb-2">
                      {{item.city}}</div>
                    <div>
                      {{item.description}} <br>
                      <v-chip variant="elevated" class="mt-4 mr-2" v-for="(item, i) in item.types" :key="i" :value="item" color="#f4a261" text-color="white">{{item}}</v-chip>
                    </div>
                  </v-card-text>
                </v-card>
              </div>
            </v-list-item-title>
          </v-list-item>
        </v-list>
      </v-card>
    </v-col>
    <v-col class="map_col" no-gutters>
      <div style="height:87vh">
        <l-map
          ref="map"
          :zoom="zoom"
          min-zoom="6"
          :center="center"
          :useGlobalLeaflet="false"
          @update:zoom="zoomUpdated"
          @update:center="centerUpdated">
          <LCircle
            v-for="marker in markers"
            :key="marker.name"
            :lat-lng="marker.position"
            color="transparent"
            fillColor="red"
            fillOpacity="0.25"
            :radius="radius"
            @click="searchItem = marker.city.toLowerCase()"
          ></LCircle>
          <l-tile-layer
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
            layer-type="base"
            name="OpenStreetMap"
          ></l-tile-layer>
        </l-map>
      </div>
    </v-col>
  </v-row>
</template>

<script>
import axios from 'axios';
import "leaflet/dist/leaflet.css";
import { LMap, LTileLayer, LCircle } from "@vue-leaflet/vue-leaflet";
import ActivityDetail from './ActivityDetail.vue';
export default {
  components: {
    LMap,
    LTileLayer,
    LCircle,
    ActivityDetail,
  },
  props: {
    searchItem: String,
  },
  data () {
    return {
      zoom: 6,
      radius: 20000,
      center: [47, 2.213749],
      search: '',
      items: [
        { text: 'Real-Time', icon: 'mdi-clock' },
        { text: 'Audience', icon: 'mdi-account' },
        { text: 'Conversions', icon: 'mdi-flag' },
      ],
      activities: [],
      markers: [],
      filterSearch: ["name", "description", "city"],
      showDetail: false,
      detailData: null,
    }
  },
  methods: {
    async getData() {
      try {
        const response = await axios.get("http://localhost:3001/api/activities", {
      'Access-Control-Allow-Origin': '*',
      'Content-Type': 'application/json',
    });
        this.activities = response.data;
        this.displayLoc();
      } catch (error) {
        console.log(error);
      }
    },
    async refreshData() {
      try {
        const response = await axios.get("http://localhost:3001/api/activities", {
        'Access-Control-Allow-Origin': '*',
        'Content-Type': 'application/json',
      });
        this.activities = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    displayLoc(){
      for (const x in this.activities) {
        const line = this.activities[x]
        if ((line.position !== undefined) && (line.position.latitude !== null && line.position.longitude !== null)){
          const obj = {
            name: line.name,
            city: line.city,
            position: [line.position.latitude, line.position.longitude]
          }
          this.markers.push(obj)
        }
      }
    },
    getDetail(item){
      this.detailData = item;
      this.showDetail = true;
    },
    async getCities() {
      try {
        const response = await axios.get("https://geo.api.gouv.fr/communes", {
      'Access-Control-Allow-Origin': '*',
      'Content-Type': 'application/json',
    });
    const citiesName = response.data.map((e) => {
      return e.nom
        
    })
    this.$store.state.citiesName = citiesName
    this.radius = 5000 * (11-this.zoom)
      } catch (error) {
        console.log(error);
      }
    },
    zoomUpdated (zoom) {
      if (zoom > 5 && zoom <= 9){
        this.radius = 5000 * (11-zoom)
      }
      else if (zoom > 9 && zoom <= 14){
        this.radius = 1000 * (15-zoom)
      }
     this.zoom = zoom;
     
   },
   centerUpdated (center) {
     this.center = center;
   }
  },
  created() {
    this.getCities();
    this.getData();
  },
}
</script>

<style>
/* width */
::-webkit-scrollbar {
  width: 6px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #FFFFFF;
  border: #F4F4F4 1px solid;
  border-radius: 2px;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #D9D9D9;
  border-radius: 4px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #BCBCBC;
}

.image_fix{
  width: 300px;
  aspect-ratio: 16/9;
  display: block;
}

.list_col{
  min-width: 300px;
  max-width: 700px;
  width: 100%;
}

.map_col{
  max-width: 1200px;
  width: 100%;
}
</style>
