<template>
  <navBar class="navBar" @setSearchItem="setSearchItem"/>
  <HelloWorld class="HelloWorld" :searchItem="searchItem"/>
</template>

<script setup>
  import navBar from '@/components/navBar.vue'
  import HelloWorld from '@/components/HelloWorld.vue'
</script>

<script>
export default{
  data () {
    return {
      searchItem: '',
    }
  },
  methods: {
    setSearchItem(search){
      this.searchItem = search;
    }
  }
}
</script>

<style>
  .navBar{
    position: sticky;
    z-index: 10;
  }

  .HelloWorld{
    position: sticky;
  }
</style>
