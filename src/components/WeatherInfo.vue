<template>
  <div class="container">
    <div class="inner-container">
        <div class="description-display">
          <div class="display-info-stats">
              <p><span>Humidity: </span>{{ humidity }}<span>%</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Dewpoint: </span>{{ dewPoint }}<span>&#176;</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Pressure: </span>{{ pressure }}<span>mb</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Wind: </span>{{ windDirection }}<span>&#176; </span><span>{{ windSpeed }}</span><span>kph</span></p>
          </div>
          <div v-if = "gustPresent" class="display-info-stats">
              <p><span>Gust: </span>{{ windGust }}<span>kph</span></p>
          </div>
          <div v-if = "noGust" class="display-info-stats">
              <p><span>Gust: none</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Heat Index: </span>{{ heatIndex }}<span>&#176;</span></p>
          </div>
        </div>
        <!--Original Description-Display-->
        <div class="description-display">
          <div class="title"><h2>{{ locationName }}</h2></div>
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


        </div>
    </div>
    <div class="searchbox">
      <div class="input-row">
          <div class="input-container">
                <label for= "latitude">Latitude:</label><p><span>&#160;&#160;&#160;</span></p>
                <input type="text" name = "latitude" v-model = "latitude">
            </div>
          <div class="input-container">
                <label for= "longitude">Longitude:</label>
                <input type="text" name = "longitude" v-model = "longitude">
            </div>
        </div>
                    <button @click="GetWeatherDataLatLon()">Get Data</button>
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
        gustPresent: true,
        noGust: false,
        temperature: '',
        latitude: '6.1128',
        longitude: '125.1717'
      }
    },
    methods:{
      GetWeatherDataLatLon(){
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=' + this.latitude + '&lon=' +this.longitude + '&appid=35c443852ab1e61c6354ecd647733a3f&units=metric').then(
          response => {
            console.log(response.data)
            let mainDescription = response.data.weather[0].main;
            let descriptionString = response.data.weather[0].description;
            this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
            
            this.locationName = response.data.name;
            this.temperature = response.data.main.temp;
            this.humidity = response.data.main.humidity;

            //Dewpoint Calculations utilizing previous API calls for temperature and humidity
            this.dewPoint = (this.temperature - ((100 - this.humidity)/5)).toFixed(2);
            
            this.heatIndex = response.data.main.feels_like;
            this.pressure = response.data.main.pressure;
            this.windSpeed = response.data.wind.speed;
            this.windDirection = response.data.wind.deg;
            this.windGust = response.data.wind.gust;

            if (this.windGust == null) {
                this.gustPresent = false;
                this.noGust = true;
            }
    
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
      this.GetWeatherDataLatLon();
      this.GetWeatherDataPlaceName();
    },
}
</script>

<style>
.container  {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;

}

.inner-container {
    width: 50%;
    background-color: rgb(45, 45, 175);
    display: flex;
    justify-content: center;
    margin: auto;
}
.description-display {
    width: 50%;
    background-color: rgb(185, 186, 176);
    justify-content: center;
    margin: 2px 2px 2px 2px;
}

.weather-description img {
  max-height: 7rem;
}

.display-info-stats {
  display: flex;
  justify-content: left;
  padding-left: 10px;
}

.searchbox {
  width: 50%;
  margin: auto;
  padding-top: 10px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  background-color: rgb(45 45 175);
  
}

.searchbox label {
  color: white;
}

.input-row {
    display: flex;
    flex-direction: column;
    width: 100%; /* Match parent width */
    align-items: flex-start; /* Align children to the start */
}

.input-container {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  margin-left: 10px;
}



.input-container input {
  margin-left: 20px;
}

input-container label {
  width: 50%;
  text-align: right;
  margin-right: 10px;
}

.label-spacer {
  width: 50%;
}

button {
  align-self: center;
  margin-bottom: 10px;
}
</style>