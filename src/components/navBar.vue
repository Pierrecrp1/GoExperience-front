<script setup>

</script>


<template>
      <div class="d-flex flex-row container-searchbarv2 justify-space-between pa-4 px-8">
        <div class="d-flex flex-row container-left justify-space-between">
          <v-img    
            :height="50"
            aspect-ratio="1/1"
            src="logo.png"
            class="mr-12"
            >
          </v-img>
          <div class="searchfield d-flex">
            <v-text-field 
              hide-details
              variant="outlined"
              label="Recherche"
              v-model="searchItem"
              @keyup.enter="this.$emit('setSearchItem', this.searchItem.toLowerCase());"
            /> 
            <v-btn color="#f4a261" variant="flat" height="100%" @click="this.$emit('setSearchItem', this.searchItem.toLowerCase());">
              Rechercher
            </v-btn>
          </div>
        </div>
        <div class="d-flex flex-row container-right">
          <v-row justify="center" :class="{ w0: !dialog }">
              <v-dialog v-model="dialog" width="1024">
                <v-card>
                  <v-card-title>
                    <span class="text-h5 mx-6">Nouvelle Activité</span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12">
                          <v-text-field
                            label="Activité"
                            required
                            v-model="activite"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12">
                          <v-textarea
                          v-model="description"
                          counter
                          required
                          label="Description"
                          maxlength="200"
                          single-line
                        ></v-textarea>
                        </v-col>
                        <v-col
                          cols="12"
                          sm="6"
                        >
                        <v-autocomplete
                          v-model="city"
                          :items="visibleItems"
                          id="city_label"
                          label="Ville"
                          :total-items="totalItems"
                          @input="loadMoreItems"
                        ></v-autocomplete>
                        </v-col>
                        <v-col
                          cols="12"
                          sm="6"
                        >
                          <v-autocomplete
                            :items="['Aquatique', 'Course', 'Jeux pour enfants', 'Vélo', 'BMX/Skate/Trotinette/Rollers', 'Football', 'Basketball', 'Pêche', 'Bowling', 'Lasergame', 'Paintball', 'Karting', 'Escalade', 'Piscine', 'Patinoire', 'Escape game', 'Plage', 'Musée','Monument','Jardins','Parc forestier','Parc aquatique' ,'Zoo','Boutique','Cinéma','Théâtre','Bar','Club','Bien-être','Médecine','Soins','Santé','Salles de jeux']"
                            label="Types"
                            multiple
                            chips
                            v-model="types"
                          ></v-autocomplete>
                        </v-col>
                      </v-row>
                      <v-row>
                        <v-col>
                          <v-file-input
                            v-model="image"
                            :rules="rulesImg"
                            accept="image/png, image/jpeg"
                            placeholder="Choisissez une Image"
                            prepend-icon="mdi-camera"
                            label="Importer une Image"
                          ></v-file-input>
                        </v-col>
                      </v-row>
                    </v-container>
                    <small>Seul l'image n'est pas requis</small>
                  </v-card-text>
                  <v-card-actions>
                    <medium v-if="errorActivity===true"><span style="color: red;">Merci de renseigner les champs nécessaires</span></medium>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="red"
                      variant="text"
                      @click="{dialog=false; errorActivity=false}"
                    >
                      Fermer
                    </v-btn>
                    <v-btn
                      color="blue-darken-1"
                      variant="text"
                      @click="addActivity"
                    >
                      Enregistrer
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-row>
          <v-btn v-if="logged" variant="outlined" height="100%" @click="{dialog=true; errorActivity=false}">
              Publier une activité
          </v-btn>
          <v-btn v-if="logged" class="ml-4" variant="outlined" height="100%" @click="logout" color="red" font-color="white">
              DECONNEXION
          </v-btn>
          <v-btn v-if="!logged" class="ml-4" variant="outlined" height="100%" href="/login" color="green" font-color="white">
              CONNEXION
          </v-btn>
        </div>
      </div>
</template>

<style>
.w0{
  width: 0px !important;
}

.test{
  min-width: 350px;
  max-width: 400px;
}

.container-right{
  min-width: 300px;
}

.container-left{
  min-width: 600px;
}
.searchfield{
  max-width: 500px;
  width: 100%;
}

.container-searchbarv2{
  box-shadow: 0px 0px 15px 0px #808080;
}

</style>

<script>
import axios from 'axios';
import _ from 'lodash';
  export default {
    data: () => ({
      dialog: false,
      logged: localStorage.getItem('userId'),
      activite: "",
      description: "",
      types: [],
      city: null,
      visibleItems: [],
      totalItems: 0,
      search: '',
      rulesImg: [
        value => {
          return !value || !value.length || value[0].size < 5000000 || 'Votre Image doit faire moins de 5 MB!'
        },
      ],
      image: null, // Image sélectionnée
      imageData: null, // Données de l'image en base 64
      base64Data: '', // Chaîne en base 64
      latitude: null,
      longitude: null,
      errorActivity: false,
      searchItem: '',
    }),
    methods: {
      ValidForm() {
        if (this.activite !== '' && this.description !== '' && this.types !== [] && this.city !== null){
          return true
        }
        return false
      },
      async addActivity() {
        if (this.ValidForm()){
          await this.getCoordinates();
          try {
            const response = await axios.post("http://localhost:3001/api/activities", {
              "name": this.activite,
              "types": this.types,
              "description": this.description,
              "city": this.city,
              "image": this.base64Data,
              "position": {
                "latitude": this.latitude,
                "longitude": this.longitude
              },
              "id_owner": localStorage.getItem('userId')
            });
            this.errorActivity = false;
            this.$forceUpdate();
            this.$router.go();
          } catch (error) {
            console.log(error);
          }
        }
        else {
          this.errorActivity = true;
        }
      },
      logout() {
        localStorage.clear();
        this.logged = false;
      },
      loadMoreItems() {
        this.search = document.getElementById("city_label").value
        const startIndex = 0;
        const endIndex = Math.min(
          startIndex + 10,
          this.filteredItems.length
        );
        this.visibleItems = this.filteredItems.slice(startIndex, endIndex);
        this.totalItems = this.filteredItems.length;
      },
      async createBase64Image() {
        if (this.image) {
          const maxSizeInBytes = 500 * 1024; // 500 Ko
          const compressedDataUrl = await this.compressImage(this.image[0], maxSizeInBytes);
          console.log(compressedDataUrl)
          this.$data.base64Data = compressedDataUrl
        }
        else{
          this.base64Data = ''
        }
      },
      compressImage(imageFile, maxSizeInBytes) {
        return new Promise((resolve, reject) => {
          const reader = new FileReader();
          reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
              const canvas = document.createElement('canvas');
              const ctx = canvas.getContext('2d');

              const compressionRatio = Math.sqrt(maxSizeInBytes / (img.width * img.height));
              const width = img.width * compressionRatio;
              const height = img.height * compressionRatio;

              canvas.width = width;
              canvas.height = height;
              ctx.drawImage(img, 0, 0, width, height);

              const compressedDataUrl = canvas.toDataURL('image/jpeg', 0.7);

              resolve(compressedDataUrl);
            };
            img.onerror = function(error) {
              reject(error);
            };
            img.src = event.target.result;
          };
          reader.onerror = function(error) {
            reject(error);
          };
          reader.readAsDataURL(imageFile);
        });
      },
      async getCoordinates() {
        try {
          // const response = await axios.get("https://nominatim.openstreetmap.org/search", {
          const response = await axios.get(`http://localhost:3001/api/search/${this.city}`, {
            headers:{
              'Content-Type': 'application/json',
            }
          });
          console.log(response);
          this.latitude = response.data.lat !== undefined ? parseFloat(response.data.lat) : null;
          this.longitude = response.data.lon !== undefined ? parseFloat(response.data.lon) : null;
          console.log(this.latitude)
          console.log(this.longitude)
        } catch (error) {
          console.log(error);
        }
      }
    },
    computed: {
    filteredItems() {
      if (this.search !== '') {
        return this.$store.state.citiesName.filter(item =>
          item.toLowerCase().includes(this.search.toLowerCase())
        );
      }
      return this.$store.state.citiesName;
    },
  },
  watch: {
    image: function(){
      this.createBase64Image()
    }
  }
  }
</script>