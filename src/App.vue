<template>
    <div class="title-logo"> 
      <img alt="Weather App Logo" src="./assets/titleImageOne.png">
    </div>
    <WeatherInfo/>
</template>

<script>

import WeatherInfo from './components/WeatherInfo.vue'
export default {
  name: 'App',
  components: {
      WeatherInfo
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

.title-logo img {
  max-height: 25rem;
}
</style>
