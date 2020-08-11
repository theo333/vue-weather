<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input type="text" class="search-input" placeholder="Enter city name..." v-model="query" />
        <button v-on:click="getWeather()" class="btn-search">Get Weather</button>
      </div>
      <div class="weather-wrapper" v-if="typeof weatherResults.weather != 'undefined'">
        <div class="location-box">
          <p class="location">
            {{ weatherResults.name }},
            {{ weatherResults.sys.country }}
          </p>
          <p class="date">date goes here - 8/10/20</p>
        </div>
        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weatherResults.main.temp) }}&deg;
            {{ this.units === "imperial" ? " F" : " C" }}
          </div>
          <div class="weather">{{ weatherResults.weather[0].main }}</div>
          <img v-bind:src="createImgUrl(weatherResults.weather[0].icon)" />
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import axios from "axios";
// http://openweathermap.org/img/wn/10d@2x.png

export default {
  name: "App",
  data() {
    return {
      api_Key: "6fe9efe3e60884da2f8b6c17c0be24f8",
      base_url: "https://api.openweathermap.org/data/2.5/",
      img_base_url: "http://openweathermap.org/img/wn/",
      img_base_url_suffix: "@2x.png",
      units: "imperial",
      query: "",
      weatherResults: {},
    };
  },
  methods: {
    getWeather() {
      axios
        .get(
          `${this.base_url}weather?q=${this.query}&APPID=${this.api_Key}&units=${this.units}`
        )
        .then((resp) => {
          this.weatherResults = resp.data;
          console.log(this.weatherResults);
        });
    },
    createImgUrl(icon) {
      return this.img_base_url + icon + this.img_base_url_suffix;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url("./assets/images/lucie-marchant-np8j3ARZjdk-unsplash.jpg");
  background-size: cover;
  background-position: top;
}

main {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 20px;
  transition: 0.5s;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.05),
    rgba(0, 0, 0, 0.15)
  );
  color: yellow;
}

.search-box {
  width: 100%;
  margin-bottom: 20px;
}

.search-input,
.btn-search {
  padding: 5px;
  border-radius: 5px;
}

.search-input {
  min-width: 200px;
}

.btn-search {
  margin-left: 10px;
}

.location-box .location {
  font-size: 2rem;
}

.location-box .date {
  font-size: 1.3rem;
}

.weather-box {
  display: inline-block;
}

.weather-box .temp {
  color: yellow;
  background-color: rgba(0, 0, 0, 0.25);
  font-size: 3rem;
  border-radius: 10px;
  margin: 15px;
  padding: 5px 15px;
}
</style>
