<template>
  <div class="container">
    <div class="inner-container">
        <button @click="GetWeatherData()">Get Data</button>
    </div>
  </div>
  <div class="container">
    <div class="description-display">
      {{ weatherDescription }}
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
      return{
        weather: {},
        weatherDescription: ''
      }
    },
    methods:{
      GetWeatherData(){
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=6.1128&lon=125.1717&appid=35c443852ab1e61c6354ecd647733a3f').then(
          response => {
            console.log(response.data.weather[0].description)
            this.weatherDescription = response.data.weather[0].description;
          }
        ).catch(error => {console.log(error)})
      }
    }
}
</script>

<style>
.container  {
    width: 100%;
    display: flex;
    justify-content: center;

}

.inner-container {
    width: 25%;
    background-color: rgb(0, 204, 255);
}
.description-display {
    width: 25%;
    background-color: rgb(0, 204, 255);
}
</style>