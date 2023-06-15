<template>
    <v-dialog
      v-model="dialog"
      persistent
      width="1024"
    >
      <v-card>
        <v-card-text>
            <v-container>
                <v-row>
                    <v-col cols="12">
                        <img v-if="!data.image" src="https://cdn.vuetifyjs.com/images/parallax/material.jpg" class="image_detail">
                        <img v-else-if="data.image!==''" :src="data.image" class="image_detail">
                    </v-col>
                </v-row>
                <v-row>
                    <v-col>
                        <div class="text-md-h3 mb-2">
                            {{data.name}}
                        </div>
                        <div class="text-md-h5 mb-2">
                            {{data.city}}
                        </div>
                        <div class="d-flex align-end mb-6">
                            <v-icon class="me-1" icon="mdi-heart" color="pink" v-if="((islike === null && data.likes.includes(getUser())) || islike===true)" @click="{islike = false; like()}"></v-icon>
                            <v-icon class="me-1" icon="mdi-heart-outline" v-else @click="{islike = true; like()}"></v-icon>
                            <span class="subheading me-2">{{data.likes.length}}</span>
                        </div>
                        <div>
                            <p v-for="line in data.description">{{ line }}</p><br>
                        <v-chip variant="elevated" class="mt-4 mr-2" v-for="(item, i) in data.types" :key="i" :value="item" color="#f4a261" text-color="white">{{item}}</v-chip>
                        </div>
                    </v-col>
                </v-row>
            </v-container>
        </v-card-text>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
                color="blue"
                variant="text"
                @click="{islike = null; $emit('closeDetail')}"
            >
                Fermer
            </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>

<script>
import axios from 'axios';
    export default{
        props: {
            detail: Boolean,
            data: Object,
        },
        data () {
            return {
                dialog: this.detail,
                islike: null,
            }
        },
        watch: {
            detail: function() {
                this.dialog = this.detail;
            }
        },
        methods: {
            async like() {
                try {
                    const response = await axios.post(`http://localhost:3001/api/activities/${this.data['_id']}/like`, {
                    "userId": localStorage.getItem('userId')
                    });
                } catch (error) {
                    console.log(error);
                }
            },
            getUser(){
                return localStorage.getItem('userId')
            },
        },
    }
</script>

<style>
.detailModal{
    display: block;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 70%;
    height: 100%;
    z-index: 10000;
    background-color: aqua;
    color: aliceblue;
    overflow: hidden;
}

.image_detail{
  width: 500px;
  aspect-ratio: 16/9;
  display: block;
  object-fit: cover;
  margin: auto;
}
</style>