<template>
  <v-container class=" corpo text-center">    
    <router-view></router-view>
  </v-container>
</template>

<script>
export default {
  name:'App',

}
</script>

<style scoped>
  .corpo{
    height: 100vh;
    margin: 2em auto;
  }
</style>