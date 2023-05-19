<template>
  <navBar class="navBar"/>
  <HelloWorld/>
</template>

<script setup>
  import navBar from '@/components/navBar.vue'
  import HelloWorld from '@/components/HelloWorld.vue'
</script>

<style>
  .navBar{
    position: sticky;
    z-index: 10;
  }
</style>
