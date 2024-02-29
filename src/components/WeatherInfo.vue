<template>
  <div class="container">
    <div class="inner-container">
        <button @click="GetWeatherData()">Get Data</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
      return{
        weather: {}
      }
    },
    methods:{
      GetWeatherData(){
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=6.1128&lon=125.1717&appid=35c443852ab1e61c6354ecd647733a3f').then(
          response => {
            console.log(response.data.weather[0])
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
    width: 50%;
    background-color: blue;
}
</style>