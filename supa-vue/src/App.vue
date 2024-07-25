<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <!-- <UserDisplay /> -->
  <UserAuth />
</template>

<script>
//import UserDisplay from './components/userdisplay.vue'
import UserAuth from './components/userAuth.vue'

export default {
  name: 'App',
  components: {
    //UserDisplay,
    UserAuth
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
