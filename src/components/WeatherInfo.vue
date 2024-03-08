<template>
  <div class="container">
    <div class="inner-container">
        <div class="description-display">
          <div class="display-info-stats">
              <p><span>Humidity: </span>{{ humidity }}<span>%</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Dewpoint: </span>{{ dewPoint }}<span>&#176;</span>{{ tempScale }}</p>
          </div>
          <div class="display-info-stats">
              <p><span>Pressure: </span>{{ pressure }}<span>mb</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Wind: </span>{{ windDirection }}<span>&#160;</span><span>{{ windSpeed }}</span><span>{{speedScale}}</span></p>
          </div>
          <div v-if = "gustPresent" class="display-info-stats">
              <p><span>Gust: </span>{{ windGust }}<span>{{ speedScale }}</span></p>
          </div>
          <div v-if = "noGust" class="display-info-stats">
              <p><span>Gust: none</span></p>
          </div>
          <div class="display-info-stats">
              <p><span>Heat Index: </span>{{ heatIndex }}<span>&#176;</span>{{ tempScale }}</p>
          </div>
        </div>
        <!--Original Description-Display-->
        <div class="description-display">
          <div class="title"><h2>{{ locationName }}</h2></div>
          <div class="display-info">
              <h3>{{ countryCodeDisplay }}</h3>
          </div>
          <div class="display-info">
              <h2>{{ temperature }}<span>&#176;</span>{{ tempScale }}</h2>
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
    <div class="searchbox-row">
      <div class="input-row-units">
        <div class="input-container-units">
          <label for="imperial-units">Imperial (US):</label>
          <input type="radio" name="units" @click="showImperial" value="imperial">
        </div>
      </div>
      <div class="input-row-units">
        <div class="input-container-units">
          <label for="metric-units">Metric:</label>
          <input type="radio" name="units" @click="showMetric" value="metric">
        </div>
      </div>
    </div>
    <div class="searchbox">
      <div class="input-row">
          <div class="input-container">
                <label for= "latitude">Latitude:</label><p><span>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;</span></p>
                <input type="text" name = "latitude" v-model = "latitude">
            </div>
          <div class="input-container">
                <label for= "longitude">Longitude:</label><p><span>&#160;&#160;&#160;&#160;&#160;&#160;&#160;</span></p>
                <input type="text" name = "longitude" v-model = "longitude">
            </div>
        </div>
                    <button @click="GetWeatherDataLatLon()">Get Weather Report</button>
      </div>
      <div class="searchbox">
      <div class="input-row">
        <div class="input-container">
          <label for="cityName">City Name:</label><p><span>&#160;&#160;&#160;&#160;&#160;&#160;</span></p>
          <input type="text" name="cityName" v-model="cityName">
        </div>
        <div class="input-container">
          <label for="stateCode">State Code:</label><p><span>&#160;&#160;&#160;&#160;&#160;</span></p>
          <input type="text" name="stateCode" v-model="stateCode">
        </div>
        <div class="input-container">
          <label for="countryCode">Country Code:</label><p><span></span></p>
          <input type="text" name="countryCode" v-model="countryCode">
        </div>
      </div>
      <button @click="GetWeatherDataPlaceName()">Get Weather Report</button>
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
        windDirection: '',
        units: 'metric',
      }
    },
    methods:{
      GetWeatherDataPlaceName() {
      const apiKey = 'a74f7a8c5a531b2988852a01586d8882';
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
      showImperial() {
        this.units = 'imperial';
        this.GetWeatherDataLatLon();
      },
      showMetric() {
        this.units = 'metric';
        this.GetWeatherDataLatLon();
      },
      GetWeatherDataLatLon(){
        const apiKey = 'a74f7a8c5a531b2988852a01586d8882';
        axios.get('https://api.openweathermap.org/data/2.5/weather?lat=' + this.latitude + '&lon=' +this.longitude + '&appid=' + apiKey +'&units=metric').then(
          response => {
            console.log(response.data)
            let mainDescription = response.data.weather[0].main;
            let descriptionString = response.data.weather[0].description;
            this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
            
            this.locationName = response.data.name;
            this.countryCodeDisplay = response.data.sys.country;

            this.humidity = response.data.main.humidity;

            if(this.units == 'metric'){
              this.temperature = response.data.main.temp;
              //Dewpoint Calculations utilizing previous API calls for temperature and humidity
              this.dewPoint = (this.temperature - ((100 - this.humidity)/5)).toFixed(2);
              this.heatIndex = response.data.main.feels_like;
              this.windSpeed = response.data.wind.speed;
              this.windGust = response.data.wind.gust;

              this.tempScale = 'C';
              this.speedScale = 'KPH';

            }

            

            if(this.units == 'imperial'){
              this.temperature = (response.data.main.temp*1.8 + 32).toFixed(2)
              this.dewPoint = ((response.data.main.temp - ((100 - this.humidity)/5))*1.8+32).toFixed(2);
              this.heatIndex = (response.data.main.feels_like*1.8 + 32).toFixed(2);
              this.windSpeed = (response.data.wind.speed * 0.62).toFixed(2);
              this.yesGust = response.data.wind.gust;

              if(this.yesGust == null){
                this.windGust == null;
              } else {
                this.windGust = (this.yesGust*0.62).toFixed(2);
              }

              this.tempScale = 'F';
              this.speedScale = 'MPH';

            }
          

            this.pressure = response.data.main.pressure;
            this.degree = response.data.wind.deg;

            console.log(this.windGust)
            
            if (this.windGust == null) {
                this.gustPresent = false;
                this.noGust = true;
            } else if (Number.isNaN(this.windGust)) {
                this.gustPresent = false;
                this.noGust = true;
            } else {
                this.gustPresent = true;
                this.noGust = false;
            }

            //Conditional Statements for NESW compass directions based on degrees
            // pulled from API wind direction data 
            if (this.degree >= 0 && this.degree < 11.25){ 
              this.windDirection = 'N';
            }
            else if (this.degree < 22.5){ 
              this.windDirection = 'NbE';
            }
            else if (this.degree < 33.75){ 
              this.windDirection = 'NNE';
            }
            else if (this.degree < 45){ 
              this.windDirection = 'NEbN';
            }
            else if (this.degree < 56.25){ 
              this.windDirection = 'NE';
            }
            else if (this.degree < 67.5){ 
              this.windDirection = 'NEbE';
            }
            else if (this.degree < 78.75){ 
              this.windDirection = 'ENE';
            }
            else if (this.degree < 90){ 
              this.windDirection = 'EbN';
            }
            else if (this.degree < 101.25){ 
              this.windDirection = 'E';
            }
            else if (this.degree < 112.5){ 
              this.windDirection = 'EbS';
            }
            else if (this.degree < 123.75){ 
              this.windDirection = 'ESE';
            }
            else if (this.degree < 135){ 
              this.windDirection = 'SEbE';
            }
            else if (this.degree < 146.25){ 
              this.windDirection = 'SE';
            }
            else if (this.degree < 157.5){ 
              this.windDirection = 'SEbS';
            }
            else if (this.degree < 168.75){ 
              this.windDirection = 'SSE';
            }
            else if (this.degree < 180){ 
              this.windDirection = 'SbE';
            }
            else if (this.degree < 191.25){ 
              this.windDirection = 'S';
            }
            else if (this.degree < 202.5){ 
              this.windDirection = 'SbW';
            }
            else if (this.degree < 213.75){ 
              this.windDirection = 'SSW';
            }
            else if (this.degree < 225){ 
              this.windDirection = 'SWbS';
            }
            else if (this.degree < 236.25){ 
              this.windDirection = 'SW';
            }
            else if (this.degree < 247.5){ 
              this.windDirection = 'SWbW';
            }
            else if (this.degree < 258.75){ 
              this.windDirection = 'WSW';
            }
            else if (this.degree < 270){ 
              this.windDirection = 'WbS';
            }
            else if (this.degree < 281.25){ 
              this.windDirection = 'W';
            }
            else if (this.degree < 292.5){ 
              this.windDirection = 'WbN';
            }
            else if (this.degree < 303.75){ 
              this.windDirection = 'WNW';
            }
            else if (this.degree < 315){ 
              this.windDirection = 'NWbW';
            }
            else if (this.degree < 326.25){ 
              this.windDirection = 'NW';
            }
            else if (this.degree < 337.5){ 
              this.windDirection = 'NWbN';
            }
            else if (this.degree < 348.75){ 
              this.windDirection = 'NNW';
            }
            else{ 
              this.windDirection = 'NbW';
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

.searchbox-row {
  width: 50%;
  margin: auto;
  margin-top: 5px;
  padding-top: 10px;
  padding-bottom: 10px;
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  background-color: rgb(255,215,0, 0.5);
  
}

.searchbox label {
  color: white;
}

.weather-description p {
  color: white;
}

.input-row {
    display: flex;
    flex-direction: column;
    width: 100%; /* Match parent width */
    align-items: flex-start; /* Align children to the start */
}

.input-row-units {
    display: flex;
    flex-direction: row;
    width: 50%; /* Match parent width */
    align-items: flex-start; /* Align children to the start */
}

.input-container {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  margin-left: 5px;
}

.input-container-units {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
  margin-left: 5px;
}

.input-container-units label{
  text-align: right;
  margin-right: 10px;
  color: white;
}


.input-container input {
  margin-left: 20px;
  width: 50%
}

/*input-container label {
  width: 50%;
  text-align: right;
  margin-right: 10px;
}*/

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

.searchbox-row {
  width: 80%;

}

}
</style>