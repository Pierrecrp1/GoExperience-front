<script setup>


</script>


<template>

    <v-container fluid class="container-login ma-0 pa-0">
        <v-row no-gutters class="container-mapper">
            <v-col cols="8">
                <!-- Ajouter vos trucs à gauche ICI -->
            </v-col>            
            <v-col cols="4">
                <v-sheet 
                class="pa-6 mx-auto d-flex flex-column align-center justify-center" 
                color="white" 
                height="100%" 
                width="100%">
                    <v-container fluid class="d-flex flex-column">
                        <h3 class="head-title">
                            Bienvenue sur Go Expérience !
                        </h3>
                        <v-form class="mt-5">
                            <v-text-field
                                variant="outlined"
                                label="Email"    
                                type="text"
                                v-model="email"
                            />
                            <v-text-field
                                variant="outlined"
                                hide-details
                                label="Mot de Passe"    
                                type="password"
                                v-model="password"
                            />
                            <v-text-field
                                variant="outlined"
                                hide-details
                                label="Confirmez le Mot de Passe"    
                                type="password"
                                class="mt-5"
                            />
                        </v-form>
                        <v-row no-gutters class="mt-5 d-flex container-buttons">
                            <v-btn color="warning" flat href="/login">
                                Déja enregistré ?
                            </v-btn>
                            <v-spacer></v-spacer>
                            <v-btn color="success" @click="register">
                                Je m'inscris
                            </v-btn>
                        </v-row>
                    </v-container>
                </v-sheet>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
import axios from 'axios';
export default {
    data () {
    return {
      email: "",
      password: ""
    }
  },
  methods: {
    async register() {
      try {
        const response = await axios.post("http://localhost:3001/api/users", {'email':this.email, 'password':this.password});
        console.log(response.data);
        localStorage.token = response.data.token;
        localStorage.userId = response.data.userId;
        
        this.$router.push({ path: '/' })
      } catch (error) {
        console.log(error);
      }
    },
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat&display=swap');

#app{
    height: 100vh;
}

.head-title{
    font-size: x-large;
    font-family: 'Montserrat', sans-serif;
;
}

/* .wrap-container{
    max-width: 600px;
    max-height: 600px;
}

.container-login{
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
} */

.container-login{
    background-color: #ec7263;
    height: 100vh;
}


.container-mapper{
    height: 100vh;
    width: 100vw;
}


</style>