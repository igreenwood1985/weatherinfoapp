<template>
  <div class="container">
    <div class="inner-container">
        <div class="description-display">
          <div class="display-info-stats">
              <p><span>Humidity: </span>{{ humidity }}<span>%</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Dewpoint: </span>{{ dewPoint }}<span>&#176;</span>C</p>
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
              <p><span>Heat Index: </span>{{ heatIndex }}<span>&#176;</span>C</p>
          </div>
        </div>
        <!--Original Description-Display-->
        <div class="description-display">
          <div class="title"><h2>{{ locationName }}</h2></div>
          <div class="display-info">
              <h3>{{ countryCodeDisplay }}</h3>
          </div>
          <div class="display-info">
              <h2>{{ temperature }}<span>&#176;</span>C</h2>
          </div>
          <div class="display-info">
              <h3>{{ weatherDescription }}</h3>
          </div>
            <div v-if="showStormy" class="weather-description">
                <img src = ../assets/thunderstormDayNight.png alt="">
            </div>
            <div v-if="showDrizzle" class="weather-description">
                <img src = ../assets/rainDay.png alt="">
            </div>
            <div v-if="showRainy" class="weather-description">
                <img src = ../assets/showerRainDayNight.png alt="">
            </div>
            <div v-if="showSnowy" class="weather-description">
                <img src = ../assets/snowDayNight.png alt="">
            </div>
            <div v-if="showClear" class="weather-description">
                <img src = ../assets/clearSkyDay.png alt="">
            </div>
            <div v-if="showCloudy" class="weather-description">
                <img src = ../assets/scatteredClouds.png alt="">
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
      <div class="searchbox">
      <div class="input-row">
        <div class="input-container">
          <label for="cityName">City Name:</label><p><span>&#160;&#160;&#160;&#160;&#160;</span></p>
          <input type="text" name="cityName" v-model="cityName">
        </div>
        <div class="input-container">
          <label for="stateCode">State Code:</label><p><span>&#160;&#160;&#160;&#160;</span></p>
          <input type="text" name="stateCode" v-model="stateCode">
        </div>
        <div class="input-container">
          <label for="countryCode">Country Code:</label>
          <input type="text" name="countryCode" v-model="countryCode">
        </div>
      </div>
      <button @click="GetWeatherDataPlaceName()">Get Data</button>
    </div>
    <p class="copyright-notice">*Weather Icons taken from <a href = https://iconpacks.net>iconpacks.net</a>.</p>
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
        showDrizzle: false,
        showNoIcon: false,
        gustPresent: true,
        noGust: false,
        temperature: '',
        latitude: '6.1128',
        longitude: '125.1717',
        cityName: '',
        stateCode: '',
        countryCode: '',
      }
    },
    methods:{
      GetWeatherDataPlaceName() {
      const apiKey = '35c443852ab1e61c6354ecd647733a3f';
      axios.get(`https://api.openweathermap.org/geo/1.0/direct?q=${this.cityName},${this.stateCode},${this.countryCode}&limit=1&appid=` + apiKey).then(
        response => {
          if (response.data && response.data.length > 0) {
            const locationData = response.data[0];
            this.latitude = locationData.lat.toString();
            this.longitude = locationData.lon.toString();
            
            // Get the weather data using the updated coordinates
            this.GetWeatherDataLatLon();
          } else {
            console.log('Location not found');
          }
        }
      ).catch(error => { console.log(error); });
    },
      GetWeatherDataLatLon(){
        const apiKey = '35c443852ab1e61c6354ecd647733a3f';
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=' + this.latitude + '&lon=' +this.longitude + '&appid=' + apiKey +'&units=metric').then(
          response => {
            console.log(response.data)
            let mainDescription = response.data.weather[0].main;
            let descriptionString = response.data.weather[0].description;
            this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
            
            this.locationName = response.data.name;
            this.countryCodeDisplay = response.data.sys.country;
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
    

            if(mainDescription == 'Thunderstorm'){
              this.showStormy = true;
            }

            if(mainDescription == 'Drizzle'){
              this.showDrizzle = true;
            }

            if(mainDescription == 'Rain'){
              this.showRainy = true;
            }

            if(mainDescription == 'Clear'){
              this.showClear = true;
            }

            if(mainDescription == 'Clouds'){
              this.showCloudy = true;
            }
            
            switch(true){
              case mainDescription == 'Thunderstorm':
                this.showCloudy = false;
                this.showStormy = true;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Drizzle':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = true;
                this.showSnowy = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Rain':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = true;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Snow':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = true;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Clear':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = true;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                break;
              case mainDescription == 'Clouds':
                this.showCloudy = true;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                break;
              default:
                this.showNoIcon = true;

            }

          }
        ).catch(error => {console.log(error)})
      },
    mounted (){
      this.GetWeatherDataLatLon();
      this.GetWeatherDataPlaceName();
    }
  }
}
</script>

<style>
.container  {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background-image: url('~@/assets/WeatherBackground.png');
    z-index: 0;

}

.inner-container {
    width: 50%;
    background-color: rgb(45, 45, 175, 0.5);
    display: flex;
    justify-content: center;
    margin: auto;
}

.description-display {
    width: 50%;
    background-color: rgb(45, 45, 175, 0.5);
    justify-content: center;
    margin: 2px 2px 2px 2px;
    position: relative;
    z-index:1;
}

.title {
  color: white;
}

.display-info {
  color: white;
}

.weather-description img {
  max-height: 7rem;
}

.display-info-stats {
  display: flex;
  justify-content: left;
  padding-left: 10px;
  color: white;
}

.searchbox {
  width: 50%;
  margin: auto;
  margin-top: 5px;
  padding-top: 10px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  background-color: rgb(255,215,0, 0.5);
  
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

button {
  align-self: center;
  margin-bottom: 10px;
}

.copyright-notice{
  color: white;
}

a {
  color: white;
  text-decoration: none;
}

a:hover, a:active {
  color: gold;
}

/*Responsive resizing of elements for smart phone screens */
@media only screen and (max-width: 650px) {
  .inner-container {
    width: 80%;

}

  .searchbox {
  width: 80%;
  
}

}
</style>