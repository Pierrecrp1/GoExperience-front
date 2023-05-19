<template>
  <v-row justify="start" no-gutters>
    <v-col cols="5" no-gutters>
      <v-card>
        <v-list style="height:90vh; overflow-y:auto;">

          <v-list-item v-for="(item, i) in activities" :key="i" :value="item" active-color="#e9c46a" @click="like(item)" variant="plain" border="10">
            <template v-slot:prepend>
              <v-img v-if="!item.image" :width="300" aspect-ratio="16/9" cover src="https://cdn.vuetifyjs.com/images/parallax/material.jpg"></v-img>
              <v-img v-if="item.image" :width="300" aspect-ratio="16/9" cover src=""></v-img>
            </template>

            <template v-slot:append class="d-flex align-end mb-6">
              <div class="d-flex align-end mb-6">
                <v-icon class="me-1" icon="mdi-heart"></v-icon>
                <span class="subheading me-2">{{item.likes.length}}</span>
              </div>
            </template>

            <v-list-item-title>
              <div class="d-flex justify-center">
                <v-card width="500px">
                  <v-card-title class="text-h6 text-md-h5 text-lg-h4">{{item.name}}</v-card-title>
                  <v-card-text>
                    <div>
                      {{item.description}} <br>
                      <v-chip variant="elevated" class="mt-6 mr-2" v-for="(item, i) in item.types" :key="i" :value="item" color="#f4a261" text-color="white">{{item}}</v-chip>
                    </div>
                    
                  </v-card-text>
                </v-card>
              </div>
            </v-list-item-title>


          </v-list-item>
        </v-list>
      </v-card>
    </v-col>
    <v-col no-gutters>
      <!-- <ol-map
        :loadTilesWhileAnimating="true"
        :loadTilesWhileInteracting="true"
        style="height:90vh"
      >
        <ol-view
          ref="view"
          :center="center"
          :rotation="rotation"
          :zoom="zoom"
          :projection="projection"
        />

        <ol-tile-layer>
          <ol-source-osm />
        </ol-tile-layer>
      </ol-map> -->
      <div style="height:90vh">
        <l-map ref="map" zoom=6 :center="[47, 2.213749]" :useGlobalLeaflet="false">
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
import { LMap, LTileLayer } from "@vue-leaflet/vue-leaflet";
export default {
  components: {
    LMap,
    LTileLayer,
  },
  data () {
    return {
      zoom: 2,
      search: '',
      items: [
        { text: 'Real-Time', icon: 'mdi-clock' },
        { text: 'Audience', icon: 'mdi-account' },
        { text: 'Conversions', icon: 'mdi-flag' },
      ],
      activities: [],
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
      } catch (error) {
        console.log(error);
      }
    },
    async like(item) {
      try {
        const response = await axios.post(`http://localhost:3001/api/activities/${item._id}/like`, {'user':item.user}, {headers:{
      'Content-Type': 'application/json',
    }});
      this.cities = response.data
      } catch (error) {
        console.log(error);
      }
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
    //console.log(this.$store.state.citiesName)
      } catch (error) {
        console.log(error);
      }
    },
  },
  created() {
    this.getData();
    // console.log(this.$store.state.citiesName.length)
    // if(this.$store.state.citiesName.length===0){
    this.getCities()
    // }
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
</style>
