<template>
  <header>
    <h1>Sakura Weather</h1>
    <div class="spacer"></div>
    <h3>Built by <a href = "https://gensanitydev.com">Gensanity Dev</a></h3>
  </header>

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
  margin-top: 5px;
}

.title-logo img {
  max-height: 25rem;
}

header {
  z-index: 1000; /*ensures header navbar glides over all other elements during scrolling */
  background-color:rgb(45, 45, 175);
  height: 3.5rem;
  width: 100%;
  display: flex;
  justify-content: left;
  padding-left: 5rem;
  align-items: center;
  top: 0;
  margin-bottom:5px;
}

header h1 {
  font-family: Brush Script MT, sans-serif;
  color: white;
  font-weight: lighter;
  width: 15%;
}

.spacer {
  width: 50%;
}

header h3 {
  font-family: sans-serif;
  color: white;
  font-weight: lighter;
  width: 25%;
}

header a {
  color: white;
  text-decoration: none;
}

header a:hover, a:active {
  color: gold;
}

@media only screen and (max-width: 650px) {
  header {
    padding-left: 1rem;
    height: 5rem;
  }

  header h1 {
    width: 30%
    
  }

  .spacer {
    width: 20%;
  }

  header h3 {
    font-size: smaller;
    width: 40%;
  }
}
</style>
