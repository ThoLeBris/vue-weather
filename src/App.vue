<template>
  <div>
    <header>
      <div class="container">
        <div class="bloc">
          <img alt="logo" src="./assets/weather-logo.png" id="logo">
          <form class="search-location" @submit.prevent="getWeather">
            <input type="text" id="input-city" placeholder="Choose your city" class="input-city" v-model="citySearch" autocomplete="off">
          </form>
        </div>

      </div>    
    </header>
    <div class="content" >
      <h3 class="not-found" v-if="!searchResult">Renseigner une ville</h3>
      
      <main class="localisation-info" v-if="searchResult">
            <h2>Votre météo du jour:</h2>
            <div class="where">
              <h1>{{weather.cityName}}</h1>
              <p>{{weather.country}}</p>
            </div>

          <div class="day-night-container">
            <div :class="isDay ? 'day':'night'"></div>
          </div>
          
          <h2> {{weather.temperature}}°C</h2>
      </main>
        <section class="details-info" v-if="searchResult">
          
          <p>Description: {{weather.description}}</p>
          <p>Humidité: {{weather.humidity}}%</p>
          <p>Température maximum aujourd'hui: {{weather.highTemp}}°C</p>
          <p>Température minimum aujourd'hui: {{weather.lowTemp}}°C</p>
          <p>Température ressentie: {{weather.feelsLike}}°C</p>
          <p>Coordonnées GPS: {{weather.coordonatesLatitude}}, {{weather.coordonatesLongitude}}</p>

        </section>
      </div>
    </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  
  data () {
    return {
      isDay: true,
      searchResult : false,
      citySearch: "",

      weather : {
        cityName : "Bordeaux",
        country : "FR",
        temperature : 9,
        description : "Cloudy with a chance of meatballs",
        highTemp : 15,
        lowTemp: 0,
        feelsLike: "Death",
        humidity : 55,
        coordonatesLongitude : 0,
        coordonatesLatitude : 0,
      }
    }
  },
  methods:{
    getWeather: async function() {
      console.log(this.citySearch);
      const key = "683176f8931f3f1996fb71647b505e3a";
      const callURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;
          
  //? Appel à l'API avec un await dans un try
      try {
        const response = await fetch(callURL);
        const data = await response.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like)
        this.weather.humidity = Math.round(data.main.humidity);
        this.weather.coordonatesLatitude = data.coord.lat;
        this.weather.coordonatesLongitude = data.coord.lon;

        this.searchResult = true;
        // check if it's daytime or nighttime
        const dayNight = data.weather[0].icon;

        if(dayNight.includes("d")){
          this.isDay = true;
        } else{
          this.isDay = false;
        }

      } catch (error) {
        console.log(error)
        this.searchResult = false;
      }
    } 
  }
}
</script>

<style>

body{
  margin: 0px;
  padding: 0px;
  background: lightcyan;

}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0px;
  padding: 0px;
}
#logo{
  height: 200px;
  width: 200px;
  border-radius: 30px;
  margin-top: 30px;

}
.input-city{
  margin: 20px;
}
.where{
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.container{
  display: flex;
  justify-content: center;
  padding: 0px;
  margin: 0px;
  margin-top: 20px;
  height: 30vh;
}
.bloc{
  background: rgba(164, 218, 252, 0.747);
  display: flex;
  flex-flow: column;
  align-items: center;
  width: 40vw;
  border-radius: 40px;
}
.localisation-info{
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.day-night-container{
  display: flex;
  justify-content: center;
}
.day{
  height: 50px;
  width: 50px;
  background-image: url(assets/sun.png);
  background-size: cover;
  border-radius: 50px;

}
.night{
  height: 50px;
  width: 50px;
  background-image: url(assets/moon.jpg);
  border-radius: 50px;
  background-size: contain;
}
</style>
