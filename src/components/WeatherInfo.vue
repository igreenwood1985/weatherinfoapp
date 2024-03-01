<template>
  <div class="container">
    <div class="inner-container">
        <button @click="GetWeatherData()">Get Data</button>
    </div>
  </div>
  <div class="container">
    <div class="description-display">
      {{ weatherDescription }}

        <div v-if="showClear" class="weather-description">
            <img src = ../assets/clearSkyDay.png alt="">
        </div>
        <div v-if="showCloudy" class="weather-description">
            <img src = ../assets/scatteredClouds.png alt="">
        </div>
        <div v-if="showRainy" class="weather-description">
            <img src = ../assets/showerRainDayNight.png alt="">
        </div>
        <div v-if="showStormy" class="weather-description">
            <img src = ../assets/thunderstormDayNight.png alt="">
        </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
      return{
        weather: {},
        weatherDescription: '',
        showClear: false,
        showCloudy: false,
        showRainy: false,
        ShowStormy: false
      }
    },
    methods:{
      GetWeatherData(){
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=6.1128&lon=125.1717&appid=35c443852ab1e61c6354ecd647733a3f').then(
          response => {
            console.log(response.data.weather[0].description)
            let mainDescription = response.data.weather[0].main;
            this.weatherDescription = response.data.weather[0].description;

            if(mainDescription == 'Clear'){
              this.showClear = true;
            }

            if(mainDescription == 'Clouds'){
              this.showCloudy = true;
            }
            
            if(mainDescription == 'Rain'){
              this.showRainy = true;
            }

            if(mainDescription == 'Thunderstorm'){
              this.showStormy = true;
            }

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
    background-color: rgb(185, 186, 176);
}

.weather-description img {
  max-height: 7rem;
}
</style>