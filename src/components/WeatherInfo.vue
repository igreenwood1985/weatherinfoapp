<template>
  <div class="container">
    <div class="inner-container">

        <!--In Depth Weather Stats Summary Display-->
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


        <!--Broad Weather Summary Display-->
        <div class="description-display">
          <div class="title"><h2>{{ locationName }}</h2></div>
          <div class="display-info">
              <h3>{{ countryName }}</h3>
          </div>
          <div class="display-info">
              <h2>{{ temperature }}<span>&#176;</span>{{ tempScale }}</h2>
          </div>
          <div class="display-info">
              <h3>{{ weatherDescription }}</h3>
          </div>
            <div v-if="showStormy" class="weather-description">
                <img src = ../assets/realThunderstormTwo.png alt="">
            </div>
            <div v-if="showDrizzle && showDay" class="weather-description">
                <img src = ../assets/realDrizzleDay.png alt="">
            </div>
            <div v-if="showDrizzle && showNight" class="weather-description">
                <img src = ../assets/realDrizzleNight.png alt="">
            </div>
            <div v-if="showRainy" class="weather-description">
                <img src = ../assets/realRainShower.png alt="">
            </div>
            <div v-if="showSnowy" class="weather-description">
                <img src = ../assets/realSnow.png alt="">
            </div>
            <div v-if="showClear && showDay" class="weather-description">
                <img src = ../assets/realClearDay.png alt="">
            </div>
            <div v-if="showClear && showNight" class="weather-description">
                <img src = ../assets/realClearNight.png alt="">
            </div>
            <div v-if="showCloudy" class="weather-description">
                <img src = ../assets/realOverCast.png alt="">
            </div>
            <div v-if="showSunCloud && showDay" class="weather-description">
                <img src = ../assets/realPartCloudDay.png alt="">
            </div>
            <div v-if="showSunCloud && showNight" class="weather-description">
                <img src = ../assets/realPartCloudNight.png alt="">
            </div>
            <div v-if="showFog" class="weather-description">
                <img src = ../assets/realFog.png alt="">
            </div>
            <div v-if="showSquall" class="weather-description">
                <img src = ../assets/realSqualls.png alt="">
            </div>
            <div v-if="showTornado" class="weather-description">
                <img src = ../assets/realTornado.png alt="">
            </div>
            <div v-if="showNoIcon" class="weather-description">
              <p>No Icon Available</p>
            </div>


        </div>
    </div>

    <!--Radio Buttons Display Field for Imperial and Metric Units Selection-->
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


    <!--Search by Latitude and Longitude-->
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


      <!--Search by City Name, State Code, and 2-Letter Country Code-->
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


    <!--Image source attributions with weblinks-->
    <p class="copyright-notice">*<a href="https://www.freepik.com/author/vectorpocket">WeatherImages by vectorpocket</a> on Freepik</p>
    <p class="copyright-notice">*Sakura Icon taken from <a href = https://www.cleanpng.com/png-flower-icon-cherry-blossom-icon-japan-icon-7690439/#google_vignette>cleanpng.com</a>.</p>
  </div>
  
</template>

<script>
import axios from 'axios'
export default {
    data(){
      return{
        weather: {},
        weatherDescription: '',
        showDay: false,
        showNight: false,
        showClear: false,
        showCloudy: false,
        showSunCloud: false,
        showRainy: false,
        showStormy: false,
        showDrizzle: false,
        showFog: false,
        showSquall: false,
        showTornado: false,
        showNoIcon: false,
        gustPresent: true,
        noGust: false,
        temperature: '',
        latitude: '',
        longitude: '',
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
            let descriptionCode = response.data.weather[0].id;
            let sunRise = response.data.sys.sunrise;
            let sunSet = response.data.sys.sunset;
            this.localTime = response.data.dt;
            this.weatherDescription = descriptionString.charAt(0).toUpperCase() + descriptionString.slice(1);
            
            this.locationName = response.data.name;

            //Acquires country code of search location from the API
            this.countryCodeDisplay = response.data.sys.country;

            //Conditional statements to convert country code into country names for display purposes
            if (this.countryCodeDisplay == 'AD'){
              this.countryName = 'Andorra';
            } else if (this.countryCodeDisplay == 'AE'){
              this.countryName = 'United Arab Emirates';
            } else if (this.countryCodeDisplay == 'AF'){
              this.countryName = 'Afghanistan';
            } else if (this.countryCodeDisplay == 'AG'){
              this.countryName = 'Antigua and Barbuda';
            } else if (this.countryCodeDisplay == 'AI'){
              this.countryName = 'Anguilla';
            } else if (this.countryCodeDisplay == 'AL'){
              this.countryName = 'Albania';
            } else if (this.countryCodeDisplay == 'AM'){
              this.countryName = 'Armenia';
            } else if (this.countryCodeDisplay == 'AO'){
              this.countryName = 'Angola';
            } else if (this.countryCodeDisplay == 'AQ'){
              this.countryName = 'Antarctica';
            } else if (this.countryCodeDisplay == 'AR'){
              this.countryName = 'Argentina';
            } else if (this.countryCodeDisplay == 'AS'){
              this.countryName = 'American Samoa';
            } else if (this.countryCodeDisplay == 'AT'){
              this.countryName = 'Austria';
            } else if (this.countryCodeDisplay == 'AU'){
              this.countryName = 'Australia';
            } else if (this.countryCodeDisplay == 'AW'){
              this.countryName = 'Aruba';
            } else if (this.countryCodeDisplay == 'AX'){
              this.countryName = 'Aland Islands';
            } else if (this.countryCodeDisplay == 'AZ'){
              this.countryName = 'Azerbaijan';
            } else if (this.countryCodeDisplay == 'BA'){
              this.countryName = 'Bosnia and Herzegovina';
            } else if (this.countryCodeDisplay == 'BB'){
              this.countryName = 'Barbados';
            } else if (this.countryCodeDisplay == 'BD'){
              this.countryName = 'Bangladesh';
            } else if (this.countryCodeDisplay == 'BE'){
              this.countryName = 'Belgium';
            } else if (this.countryCodeDisplay == 'BF'){
              this.countryName = 'Burkina Faso';
            } else if (this.countryCodeDisplay == 'BG'){
              this.countryName = 'Bulgaria';
            } else if (this.countryCodeDisplay == 'BH'){
              this.countryName = 'Bahrain';
            } else if (this.countryCodeDisplay == 'BI'){
              this.countryName = 'Burundi';
            } else if (this.countryCodeDisplay == 'BJ'){
              this.countryName = 'Benine';
            } else if (this.countryCodeDisplay == 'BL'){
              this.countryName = 'Saint Barthélemy';
            } else if (this.countryCodeDisplay == 'BM'){
              this.countryName = 'Bermuda';
            } else if (this.countryCodeDisplay == 'BN'){
              this.countryName = 'Brunei Darussalam';
            } else if (this.countryCodeDisplay == 'BO'){
              this.countryName = 'Bolivia';
            } else if (this.countryCodeDisplay == 'BQ'){
              this.countryName = 'BES Islands (Netherlands)';
            } else if (this.countryCodeDisplay == 'BR'){
              this.countryName = 'Brazil';
            } else if (this.countryCodeDisplay == 'BS'){
              this.countryName = 'Bahamas';
            } else if (this.countryCodeDisplay == 'BT'){
              this.countryName = 'Bhutan';
            } else if (this.countryCodeDisplay == 'BV'){
              this.countryName = 'Bouvet Island (Norway)';
            } else if (this.countryCodeDisplay == 'BW'){
              this.countryName = 'Botswana';
            } else if (this.countryCodeDisplay == 'BY'){
              this.countryName = 'Belarus';
            } else if (this.countryCodeDisplay == 'BZ'){
              this.countryName = 'Belize';
            } else if (this.countryCodeDisplay == 'CA'){
              this.countryName = 'Canada';
            } else if (this.countryCodeDisplay == 'CC'){
              this.countryName = 'Cocos (Keeling) Islands (Australia)';
            } else if (this.countryCodeDisplay == 'CD'){
              this.countryName = 'Democratic Republic of the Congo';
            } else if (this.countryCodeDisplay == 'CF'){
              this.countryName = 'Central African Republic';
            } else if (this.countryCodeDisplay == 'CG'){
              this.countryName = 'Congo';
            } else if (this.countryCodeDisplay == 'CH'){
              this.countryName = 'Switzerland';
            } else if (this.countryCodeDisplay == 'CI'){
              this.countryName = 'Côte d`Ivoire';
            } else if (this.countryCodeDisplay == 'CK'){
              this.countryName = 'Cook Islands';
            } else if (this.countryCodeDisplay == 'CL'){
              this.countryName = 'Chile';
            } else if (this.countryCodeDisplay == 'CM'){
              this.countryName = 'Cameroon';
            } else if (this.countryCodeDisplay == 'CN'){
              this.countryName = 'China PRC';
            } else if (this.countryCodeDisplay == 'CO'){
              this.countryName = 'Columbia';
            } else if (this.countryCodeDisplay == 'CR'){
              this.countryName = 'Costa Rica';
            } else if (this.countryCodeDisplay == 'CU'){
              this.countryName = 'Cuba';
            } else if (this.countryCodeDisplay == 'CV'){
              this.countryName = 'Cabo Verde';
            } else if (this.countryCodeDisplay == 'CW'){
              this.countryName = 'Curaçao (Netherlands)';
            } else if (this.countryCodeDisplay == 'CX'){
              this.countryName = 'Christmas Island (Australia)';
            } else if (this.countryCodeDisplay == 'CY'){
              this.countryName = 'Cyprus';
            } else if (this.countryCodeDisplay == 'CZ'){
              this.countryName = 'Czech Republic (Czechia)';
            } else if (this.countryCodeDisplay == 'DE'){
              this.countryName = 'Germany';
            } else if (this.countryCodeDisplay == 'DJ'){
              this.countryName = 'Djibouti';
            } else if (this.countryCodeDisplay == 'DK'){
              this.countryName = 'Denmark';
            } else if (this.countryCodeDisplay == 'DM'){
              this.countryName = 'Dominica';
            } else if (this.countryCodeDisplay == 'DO'){
              this.countryName = 'Dominican Republic';
            } else if (this.countryCodeDisplay == 'DZ'){
              this.countryName = 'Algeria';
            } else if (this.countryCodeDisplay == 'EC'){
              this.countryName = 'Ecuador';
            } else if (this.countryCodeDisplay == 'EE'){
              this.countryName = 'Estonia';
            } else if (this.countryCodeDisplay == 'EG'){
              this.countryName = 'Egypt';
            } else if (this.countryCodeDisplay == 'EH'){
              this.countryName = 'Western Sahara';
            } else if (this.countryCodeDisplay == 'ER'){
              this.countryName = 'Eritrea';
            } else if (this.countryCodeDisplay == 'ES'){
              this.countryName = 'Spain';
            } else if (this.countryCodeDisplay == 'ET'){
              this.countryName = 'Ethiopia';
            } else if (this.countryCodeDisplay == 'FI'){
              this.countryName = 'Finland';
            } else if (this.countryCodeDisplay == 'FJ'){
              this.countryName = 'Fiji';
            } else if (this.countryCodeDisplay == 'FK'){
              this.countryName = 'Falkland Islands (UK)';
            } else if (this.countryCodeDisplay == 'FM'){
              this.countryName = 'Federated States of Micronesia';
            } else if (this.countryCodeDisplay == 'FO'){
              this.countryName = 'Faroe Islands (Denmark)';
            } else if (this.countryCodeDisplay == 'FR'){
              this.countryName = 'France';
            } else if (this.countryCodeDisplay == 'GA'){
              this.countryName = 'Gabon';
            } else if (this.countryCodeDisplay == 'GB'){
              this.countryName = 'United Kingdom';
            } else if (this.countryCodeDisplay == 'GD'){
              this.countryName = 'Grenada';
            } else if (this.countryCodeDisplay == 'GE'){
              this.countryName = 'Georgia';
            } else if (this.countryCodeDisplay == 'GF'){
              this.countryName = 'French Guiana';
            } else if (this.countryCodeDisplay == 'GG'){
              this.countryName = 'Guernsey (UK)';
            } else if (this.countryCodeDisplay == 'GH'){
              this.countryName = 'Ghana';
            } else if (this.countryCodeDisplay == 'GI'){
              this.countryName = 'Gibraltar (UK)';
            } else if (this.countryCodeDisplay == 'GL'){
              this.countryName = 'Greenland';
            } else if (this.countryCodeDisplay == 'GM'){
              this.countryName = 'Gambia';
            } else if (this.countryCodeDisplay == 'GN'){
              this.countryName = 'Guinea';
            } else if (this.countryCodeDisplay == 'GP'){
              this.countryName = 'Guadeloupe';
            } else if (this.countryCodeDisplay == 'GQ'){
              this.countryName = 'Equitorial Guinea';
            } else if (this.countryCodeDisplay == 'GR'){
              this.countryName = 'Greece';
            } else if (this.countryCodeDisplay == 'GS'){
              this.countryName = 'South Georgia & South Sandwich Islands (UK)';
            } else if (this.countryCodeDisplay == 'GT'){
              this.countryName = 'Guatemala';
            } else if (this.countryCodeDisplay == 'GU'){
              this.countryName = 'Guam (US)';
            } else if (this.countryCodeDisplay == 'GW'){
              this.countryName = 'Guinea-Bissau';
            } else if (this.countryCodeDisplay == 'GY'){
              this.countryName = 'Guyana';
            } else if (this.countryCodeDisplay == 'HK'){
              this.countryName = 'Hong Kong SAR';
            } else if (this.countryCodeDisplay == 'HM'){
              this.countryName = 'Heard Island and Mcdonald Islands (Australia)';
            } else if (this.countryCodeDisplay == 'HN'){
              this.countryName = 'Honduras';
            } else if (this.countryCodeDisplay == 'HR'){
              this.countryName = 'Croatia';
            } else if (this.countryCodeDisplay == 'HT'){
              this.countryName = 'Haiti';
            } else if (this.countryCodeDisplay == 'HU'){
              this.countryName = 'Hungary';
            } else if (this.countryCodeDisplay == 'ID'){
              this.countryName = 'Indonesia';
            } else if (this.countryCodeDisplay == 'IE'){
              this.countryName = 'Ireland';
            } else if (this.countryCodeDisplay == 'IL'){
              this.countryName = 'Israel';
            } else if (this.countryCodeDisplay == 'IM'){
              this.countryName = 'Isle of Man (UK)';
            } else if (this.countryCodeDisplay == 'IN'){
              this.countryName = 'India';
            } else if (this.countryCodeDisplay == 'IO'){
              this.countryName = 'British Indian Ocean Territory (UK)';
            } else if (this.countryCodeDisplay == 'IQ'){
              this.countryName = 'Iraq';
            } else if (this.countryCodeDisplay == 'IR'){
              this.countryName = 'Iran';
            } else if (this.countryCodeDisplay == 'IS'){
              this.countryName = 'Iceland';
            } else if (this.countryCodeDisplay == 'IT'){
              this.countryName = 'Italy';
            } else if (this.countryCodeDisplay == 'JE'){
              this.countryName = 'Jersey (UK)';
            } else if (this.countryCodeDisplay == 'JM'){
              this.countryName = 'Jamaica';
            } else if (this.countryCodeDisplay == 'JO'){
              this.countryName = 'Jordan';
            } else if (this.countryCodeDisplay == 'JP'){
              this.countryName = 'Japan';
            } else if (this.countryCodeDisplay == 'KE'){
              this.countryName = 'Kenya';
            } else if (this.countryCodeDisplay == 'KG'){
              this.countryName = 'Kyrgyzstan';
            } else if (this.countryCodeDisplay == 'KH'){
              this.countryName = 'Cambodia';
            } else if (this.countryCodeDisplay == 'KI'){
              this.countryName = 'Kiribati';
            } else if (this.countryCodeDisplay == 'KM'){
              this.countryName = 'Comoros';
            } else if (this.countryCodeDisplay == 'KN'){
              this.countryName = 'Saint Kitts and Nevis (UK)';
            } else if (this.countryCodeDisplay == 'KP'){
              this.countryName = 'North Korea DPRK';
            } else if (this.countryCodeDisplay == 'KR'){
              this.countryName = 'South Korea ROK';
            } else if (this.countryCodeDisplay == 'KW'){
              this.countryName = 'Kuwait';
            } else if (this.countryCodeDisplay == 'KY'){
              this.countryName = 'Cayman Islands (UK)';
            } else if (this.countryCodeDisplay == 'KZ'){
              this.countryName = 'Kazakhstan';
            } else if (this.countryCodeDisplay == 'LA'){
              this.countryName = 'Laos LDPR';
            } else if (this.countryCodeDisplay == 'LB'){
              this.countryName = 'Lebanon';
            } else if (this.countryCodeDisplay == 'LC'){
              this.countryName = 'Saint Lucia (UK)';
            } else if (this.countryCodeDisplay == 'LI'){
              this.countryName = 'Liechtenstein';
            } else if (this.countryCodeDisplay == 'LK'){
              this.countryName = 'Sri Lanka';
            } else if (this.countryCodeDisplay == 'LR'){
              this.countryName = 'Liberia';
            } else if (this.countryCodeDisplay == 'LS'){
              this.countryName = 'Lesotho';
            } else if (this.countryCodeDisplay == 'LT'){
              this.countryName = 'Lithuania';
            } else if (this.countryCodeDisplay == 'LU'){
              this.countryName = 'Luxenbourg';
            } else if (this.countryCodeDisplay == 'LV'){
              this.countryName = 'Latvia';
            } else if (this.countryCodeDisplay == 'LY'){
              this.countryName = 'Libya';
            } else if (this.countryCodeDisplay == 'MA'){
              this.countryName = 'Morocco';
            } else if (this.countryCodeDisplay == 'MC'){
              this.countryName = 'Monaco';
            } else if (this.countryCodeDisplay == 'MD'){
              this.countryName = 'Moldova';
            } else if (this.countryCodeDisplay == 'ME'){
              this.countryName = 'Montenegro';
            } else if (this.countryCodeDisplay == 'MF'){
              this.countryName = 'Saint Martin (France)';
            } else if (this.countryCodeDisplay == 'MG'){
              this.countryName = 'Madagascar';
            } else if (this.countryCodeDisplay == 'MH'){
              this.countryName = 'Marshall Islands (US)';
            } else if (this.countryCodeDisplay == 'MK'){
              this.countryName = 'North Macedonia';
            } else if (this.countryCodeDisplay == 'ML'){
              this.countryName = 'Mali';
            } else if (this.countryCodeDisplay == 'MM'){
              this.countryName = 'Myanmar';
            } else if (this.countryCodeDisplay == 'MN'){
              this.countryName = 'Mongolia';
            } else if (this.countryCodeDisplay == 'MO'){
              this.countryName = 'Macao SAR';
            } else if (this.countryCodeDisplay == 'MP'){
              this.countryName = 'Northern Mariana Islands (US)';
            } else if (this.countryCodeDisplay == 'MQ'){
              this.countryName = 'Martinique (France)';
            } else if (this.countryCodeDisplay == 'MR'){
              this.countryName = 'Mauritania';
            } else if (this.countryCodeDisplay == 'MS'){
              this.countryName = 'Montsurrat (UK)';
            } else if (this.countryCodeDisplay == 'MT'){
              this.countryName = 'Malta';
            } else if (this.countryCodeDisplay == 'MU'){
              this.countryName = 'Muritius';
            } else if (this.countryCodeDisplay == 'MV'){
              this.countryName = 'Maldives';
            } else if (this.countryCodeDisplay == 'MW'){
              this.countryName = 'Malawi';
            } else if (this.countryCodeDisplay == 'MX'){
              this.countryName = 'Mexico';
            } else if (this.countryCodeDisplay == 'MY'){
              this.countryName = 'Malaysia';
            } else if (this.countryCodeDisplay == 'MZ'){
              this.countryName = 'Mozambique';
            } else if (this.countryCodeDisplay == 'NA'){
              this.countryName = 'Namibia';
            } else if (this.countryCodeDisplay == 'NC'){
              this.countryName = 'New Caledonia (France)';
            } else if (this.countryCodeDisplay == 'NG'){
              this.countryName = 'Niger';
            } else if (this.countryCodeDisplay == 'NI'){
              this.countryName = 'Nicaragua';
            } else if (this.countryCodeDisplay == 'NL'){
              this.countryName = 'Kingdom of the Netherlands';
            } else if (this.countryCodeDisplay == 'NO'){
              this.countryName = 'Norway';
            } else if (this.countryCodeDisplay == 'NP'){
              this.countryName = 'Nepal';
            } else if (this.countryCodeDisplay == 'NR'){
              this.countryName = 'Nauru';
            } else if (this.countryCodeDisplay == 'NU'){
              this.countryName = 'Niue (New Zealand)';
            } else if (this.countryCodeDisplay == 'NZ'){
              this.countryName = 'New Zealand';
            } else if (this.countryCodeDisplay == 'OM'){
              this.countryName = 'Oman';
            } else if (this.countryCodeDisplay == 'PA'){
              this.countryName = 'Panama';
            } else if (this.countryCodeDisplay == 'PE'){
              this.countryName = 'Peru';
            } else if (this.countryCodeDisplay == 'PF'){
              this.countryName = 'French Polynesia';
            } else if (this.countryCodeDisplay == 'PG'){
              this.countryName = 'Papua New Guinea';
            } else if (this.countryCodeDisplay == 'PH'){
              this.countryName = 'Philippines';
            } else if (this.countryCodeDisplay == 'PK'){
              this.countryName = 'Pakistan';
            } else if (this.countryCodeDisplay == 'PL'){
              this.countryName = 'Poland';
            } else if (this.countryCodeDisplay == 'PM'){
              this.countryName = 'Saint Pierre and Miquelon (France)';
            } else if (this.countryCodeDisplay == 'PN'){
              this.countryName = 'Pitcairn (UK)';
            } else if (this.countryCodeDisplay == 'PR'){
              this.countryName = 'Puerto Rico (US)';
            } else if (this.countryCodeDisplay == 'PS'){
              this.countryName = 'Israel (Palestinian Territories)';
            } else if (this.countryCodeDisplay == 'PT'){
              this.countryName = 'Portugal';
            } else if (this.countryCodeDisplay == 'PW'){
              this.countryName = 'Palau';
            } else if (this.countryCodeDisplay == 'PY'){
              this.countryName = 'Paraguay';
            } else if (this.countryCodeDisplay == 'QA'){
              this.countryName = 'Qatar';
            } else if (this.countryCodeDisplay == 'RE'){
              this.countryName = 'Réunion (France)';
            } else if (this.countryCodeDisplay == 'RO'){
              this.countryName = 'Romania';
            } else if (this.countryCodeDisplay == 'RS'){
              this.countryName = 'Serbia';
            } else if (this.countryCodeDisplay == 'RU'){
              this.countryName = 'Russian Federation';
            } else if (this.countryCodeDisplay == 'RW'){
              this.countryName = 'Rwanda';
            } else if (this.countryCodeDisplay == 'SA'){
              this.countryName = 'Saudi Arabia';
            } else if (this.countryCodeDisplay == 'SB'){
              this.countryName = 'Solomon Islands (UK)';
            } else if (this.countryCodeDisplay == 'SC'){
              this.countryName = 'Seychelles';
            } else if (this.countryCodeDisplay == 'SD'){
              this.countryName = 'Sudan';
            } else if (this.countryCodeDisplay == 'SE'){
              this.countryName = 'Sweden';
            } else if (this.countryCodeDisplay == 'SG'){
              this.countryName = 'Republic of Singapore';
            } else if (this.countryCodeDisplay == 'SH'){
              this.countryName = 'Saint Helena';
            } else if (this.countryCodeDisplay == 'SI'){
              this.countryName = 'Slovenia';
            } else if (this.countryCodeDisplay == 'SJ'){
              this.countryName = 'Svalbard (Norway)';
            } else if (this.countryCodeDisplay == 'SK'){
              this.countryName = 'Slovakia';
            } else if (this.countryCodeDisplay == 'SL'){
              this.countryName = 'Sierra Leone';
            } else if (this.countryCodeDisplay == 'SM'){
              this.countryName = 'San Marino';
            } else if (this.countryCodeDisplay == 'SN'){
              this.countryName = 'Senegal';
            } else if (this.countryCodeDisplay == 'SO'){
              this.countryName = 'Somalia';
            } else if (this.countryCodeDisplay == 'SR'){
              this.countryName = 'Suriname';
            } else if (this.countryCodeDisplay == 'SS'){
              this.countryName = 'South Sudan';
            } else if (this.countryCodeDisplay == 'ST'){
              this.countryName = 'Sao Tome and Principe';
            } else if (this.countryCodeDisplay == 'SV'){
              this.countryName = 'El Salvador';
            } else if (this.countryCodeDisplay == 'SX'){
              this.countryName = 'Dutch Saint Martin (Netherlands)';
            } else if (this.countryCodeDisplay == 'SY'){
              this.countryName = 'Syria';
            } else if (this.countryCodeDisplay == 'SZ'){
              this.countryName = 'Eswatini';
            } else if (this.countryCodeDisplay == 'TC'){
              this.countryName = 'Turks and Caicos Islands (UK)';
            } else if (this.countryCodeDisplay == 'TD'){
              this.countryName = 'Chad';
            } else if (this.countryCodeDisplay == 'TF'){
              this.countryName = 'French Southern Territories';
            } else if (this.countryCodeDisplay == 'TG'){
              this.countryName = 'Togo';
            } else if (this.countryCodeDisplay == 'TH'){
              this.countryName = 'Thailand';
            } else if (this.countryCodeDisplay == 'TJ'){
              this.countryName = 'Tajikistan';
            } else if (this.countryCodeDisplay == 'TK'){
              this.countryName = 'Tokelau (New Zealand)';
            } else if (this.countryCodeDisplay == 'TL'){
              this.countryName = 'Timor-Leste';
            } else if (this.countryCodeDisplay == 'TM'){
              this.countryName = 'Turkmenistan';
            } else if (this.countryCodeDisplay == 'TN'){
              this.countryName = 'Tunisia';
            } else if (this.countryCodeDisplay == 'TO'){
              this.countryName = 'Tonga';
            } else if (this.countryCodeDisplay == 'TR'){
              this.countryName = 'Türkiye';
            } else if (this.countryCodeDisplay == 'TT'){
              this.countryName = 'Trinidad and Tobago';
            } else if (this.countryCodeDisplay == 'TV'){
              this.countryName = 'Tuvalu';
            } else if (this.countryCodeDisplay == 'TW'){
              this.countryName = 'Taiwan ROC';
            } else if (this.countryCodeDisplay == 'TZ'){
              this.countryName = 'Tanzania';
            } else if (this.countryCodeDisplay == 'UA'){
              this.countryName = 'Ukraine';
            } else if (this.countryCodeDisplay == 'UG'){
              this.countryName = 'Uganda';
            } else if (this.countryCodeDisplay == 'UM'){
              this.countryName = 'US Minor Outlying Territories';
            } else if (this.countryCodeDisplay == 'US'){
              this.countryName = 'United States of America';
            } else if (this.countryCodeDisplay == 'UY'){
              this.countryName = 'Uruguay';
            } else if (this.countryCodeDisplay == 'UZ'){
              this.countryName = 'Uzbekistan';
            } else if (this.countryCodeDisplay == 'VA'){
              this.countryName = 'Holy See';
            } else if (this.countryCodeDisplay == 'VC'){
              this.countryName = 'Saint Vincent and the Grenadines (UK)';
            } else if (this.countryCodeDisplay == 'VE'){
              this.countryName = 'Venezuela';
            } else if (this.countryCodeDisplay == 'VG'){
              this.countryName = 'British Virgin Islands';
            } else if (this.countryCodeDisplay == 'VI'){
              this.countryName = 'US Virgin Islands';
            } else if (this.countryCodeDisplay == 'VN'){
              this.countryName = 'Vietnam';
            } else if (this.countryCodeDisplay == 'VU'){
              this.countryName = 'Vanuatu';
            } else if (this.countryCodeDisplay == 'WF'){
              this.countryName = 'Wallis and Fortuna (France)';
            } else if (this.countryCodeDisplay == 'WS'){
              this.countryName = 'Samoa';
            } else if (this.countryCodeDisplay == 'YE'){
              this.countryName = 'Yemen';
            } else if (this.countryCodeDisplay == 'YT'){
              this.countryName = 'Mayotte (France)';
            } else if (this.countryCodeDisplay == 'ZA'){
              this.countryName = 'South Africa';
            } else if (this.countryCodeDisplay == 'ZM'){
              this.countryName = 'Zambia';
            } else if (this.countryCodeDisplay == 'ZW'){
              this.countryName = 'Zimbabwe';
            } else {
              this.countryName = this.countryCodeDisplay;
            }


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


            //Conditional Statements to determine daytime or nighttime at locatoin
            if(this.localTime >= sunRise && this.localTime < sunSet){
              this.showDay = true;
              this.showNight = false;
            } else {
              this.showNight = true;
              this.showDay = false;
            }


            //Conditional Statements to Check mainDescription and activate showImage Variables
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
            if(mainDescription == 'Squall'){
              this.showSquall = true;
            }
            if(mainDescription == 'Tornado'){
              this.showTornado = true;
            }

            if(mainDescription == 'Clouds' && descriptionCode == 804){
              this.showCloudy = true;
            }

            if(mainDescription == 'Clouds' && descriptionCode != 804){
              this.showSunCloud = true;
            }
            if(mainDescription == 'Fog' || mainDescription == 'Mist' || mainDescription == 'Haze'
                || mainDescription == 'Smoke' || mainDescription == 'Dust' || mainDescription == 'Ash') {
                  this.showFog = true;
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
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Drizzle':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = true;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Rain':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = true;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Snow':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = true; 
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Clear':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = true;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Clouds' && descriptionCode == 804:
                this.showCloudy = true;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Clouds' && descriptionCode != 804:
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = true;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = false;
                break;
                case mainDescription == 'Fog':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Mist':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
                case mainDescription == 'Haze':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Smoke':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Dust':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Ash':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = true;
                this.showSquall = false;
                this.showTornado = false;
                break;
              case mainDescription == 'Squall':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = true;
                this.showTornado = false;
                break;
              case mainDescription == 'Tornado':
                this.showCloudy = false;
                this.showStormy = false;
                this.showRainy = false;
                this.showClear = false;
                this.showDrizzle = false;
                this.showSnowy = false;
                this.showNoIcon = false;
                this.showSunCloud = false;
                this.showFog = false;
                this.showSquall = false;
                this.showTornado = true;
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
  background-color: rgb(192, 107, 123, 0.5);
  
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
  margin-top: 2px;
  margin-bottom: 1px;
}

a {
  color: white;
  text-decoration: none;
}

a:hover, a:active {
  color: gold;
}


/*Responsive resizing of elements for smart phone screens */
@media only screen and (max-width: 1200px) {

.weather-description img {
    max-width: 7rem;
}

}

@media only screen and (max-width: 650px) {
.inner-container {
    width: 80%;

}

.display-info-stats {
  display: flex;
  justify-content: center;
  padding-left: 20px;
  padding-right: 20px;
}

.searchbox {
  width: 80%;
  
}

.searchbox-row {
  width: 80%;

}

.weather-description img {
  max-width: 7rem;
}

}
</style>