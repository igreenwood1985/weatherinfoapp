<template>
  <div class="container">
    <div class="inner-container">
        <div class="description-display">
          <div class="display-info">
              <h2>{{ temperature }}<span>&#176;</span></h2>
          </div>
          <div class="display-info">
              <h3>{{ weatherDescription }}</h3>
          </div>
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
            <div v-if="showNoIcon" class="weather-description">
              <p>No Icon Available</p>
            </div>

            <div class="input-container">
                <label for= "latitude">Latitude</label>
                <input type="text" name = "latitude" v-model = "latitude">
            </div>
            <div class="input-container">
                <label for= "longitude">Longitude</label>
                <input type="text" name = "longitude" v-model = "longitude">
            </div>
          <button @click="GetWeatherData()">Get Data</button>
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
        showStormy: false,
        showNoIcon: false,
        temperature: '',
        latitude: '6.1128',
        longitude: '125.1717'
      }
    },
    methods:{
      GetWeatherData(){
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=' + this.latitude + '&lon=' +this.longitude + '&appid=35c443852ab1e61c6354ecd647733a3f&units=metric').then(
          response => {
            console.log(response.data)
            let mainDescription = response.data.weather[0].main;
            let descriptionString = response.data.weather[0].description;
            this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
            this.temperature = response.data.main.temp;
    
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

            switch(true){
              case mainDescription == 'Clouds':
                this.showCloudy = true;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Thunderstorm':
                this.showCloudy = false;
                this.showStormy = true;
                this.showRainy = false;
                this.showClear = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Rain':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = true;
                this.showClear = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Clear':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = true;
                this.showNoIcon = false;
                break;
              default:
                this.showNoIcon = true;

            }

          }
        ).catch(error => {console.log(error)})
      }
    },
    mounted (){
      this.GetWeatherData();
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
    justify-content: center;
    width: 50%;
    background-color: rgb(45, 45, 175);
}
.description-display {
    width: 100%;
    background-color: rgb(185, 186, 176);
}

.weather-description img {
  max-height: 7rem;
}

</style>