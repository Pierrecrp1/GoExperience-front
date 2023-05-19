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
            /> 
            <v-btn color="#f4a261" variant="flat" height="100%" @click="loadMoreItems()">
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
                    </v-container>
                    <small>Tout les champs sont requis</small>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="red"
                      variant="text"
                      @click="dialog = false"
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
          <v-btn v-if="logged" variant="outlined" height="100%" @click="dialog=true">
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
  min-width: 500px;
}

.container-searchbarv2{
  box-shadow: 0px 0px 15px 0px #808080;
}

</style>

<script>
import axios from 'axios';
  export default {
    data: () => ({
      user: {
        initials: 'JD',
        fullName: 'John Doe',
        email: 'john.doe@doe.com',
      },
      dialog: false,
      logged: localStorage.getItem('userId'),
      activite: "",
      description: "",
      types: [],
      city: '',
      visibleItems: [],
      totalItems: 0,
      search: '',

    }),
    methods: {
      async addActivity() {
        try {
          const response = await axios.post("http://localhost:3001/api/activities", {
            "name": this.activite,
            "types": this.types,
            "description": this.description,
            "id_owner": localStorage.getItem('userId')
          });
          this.$forceUpdate();
          this.$router.go()
        } catch (error) {
          console.log(error);
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
  }
</script>