<template>
  <div id="app" v-bind:class="getBackgroundImgClass(this.backgroundImgClass)">
    <main>
      <div class="search-box">
        <input type="text" class="search-input" placeholder="Enter city name..." v-model="query" />
        <button v-on:click="getWeather()" class="btn-search">Get Weather</button>
      </div>
      <div class="weather-wrapper" v-if="typeof weatherResults.weather != 'undefined'">
        <div class="location-box">
          <h1 class="location">
            {{ weatherResults.name }},
            {{ weatherResults.sys.country }}
          </h1>
          <h2 class="date">{{ createDate(weatherResults.dt) }}</h2>
        </div>
        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weatherResults.main.temp) }}&deg;
            {{ this.units === "imperial" ? " F" : " C" }}
          </div>
          <div class="conditions">
            <div class="weather-description">{{ weatherResults.weather[0].description }}</div>
            <img v-bind:src="createImgUrl(weatherResults.weather[0].icon)" />
          </div>
        </div>
      </div>
      <div class="weather-results-error" v-if="weatherResultsError != ''">
        <h3>{{ weatherResultsError }}</h3>
      </div>
    </main>
  </div>
</template>

<script>
import axios from "axios";

// TODO find better solution to hiding API key from github
import { API_KEY } from "../.env.local.js";

export default {
  name: "App",
  data() {
    return {
      baseUrl: "https://api.openweathermap.org/data/2.5/",
      imgBaseUrl: "http://openweathermap.org/img/wn/",
      imgBaseUrlSuffix: "@2x.png",
      units: "imperial",
      query: "",
      weatherResults: {},
      backgroundImgClass: "",
      weatherResultsError: "",
    };
  },
  methods: {
    getWeather() {
      axios
        .get(
          `${this.baseUrl}weather?q=${this.query}&APPID=${API_KEY}&units=${this.units}`
        )
        .then((resp) => {
          this.weatherResults = resp.data;
          this.backgroundImgClass = this.weatherResults.weather[0].main;
          this.weatherResultsError = "";
        })
        .catch((err) => {
          console.error(err);
          this.weatherResults = "";
          this.weatherResultsError =
            "Oops! Sorry, we could not find the weather for that city.  Please try again.";
          this.backgroundImgClass = "error";
        });
    },
    createImgUrl(icon) {
      return this.imgBaseUrl + icon + this.imgBaseUrlSuffix;
    },
    getBackgroundImgClass(conditions) {
      let className = "";
      switch (conditions) {
        case "Clear":
          className = "sunny";
          break;
        case "Clouds":
          className = "cloudy";
          break;
        case "Rain":
          className = "rainy";
          break;
        case "Mist":
          className = "mist";
          break;
        case "error":
          className = "error";
          break;
        default:
          return "";
      }
      return className;
    },
    createDate(unixTimestamp) {
      const dateObject = new Date(unixTimestamp * 1000);
      const dateFormat = dateObject.toLocaleString();
      return dateFormat;
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
  background-image: url("./assets/images/initial/joan-mesquida-kWJb_eJbZhc-unsplash.jpg");
  background-size: cover;
  background-position: top;
}

#app.sunny {
  background-image: url("./assets/images/sunny/kilarov-zaneit-OzRhGl0BsWU-unsplash.jpg");
}

#app.cloudy {
  background-image: url("./assets/images/cloudy/fabrizio-conti-AtHvihIObKo-unsplash.jpg");
}

#app.rainy {
  background-image: url("./assets/images/rainy/neonbrand-TtlSPDneJgM-unsplash.jpg");
}

#app.mist {
  background-image: url("./assets/images/mist/panjinda-iIqiI5fyZKs-unsplash.jpg");
}

#app.error {
  background-image: url("./assets/images/error/sarah-kilian-52jRtc2S_VE-unsplash.jpg");
}

main {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 20px;
  transition: 0.5s;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.15),
    rgba(0, 0, 0, 0.35)
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

.location-box {
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .location {
  font-size: 2rem;
  font-weight: 800;
}

.location-box .date {
  font-size: 1.3rem;
}

.weather-box {
  display: inline-block;
  /* background-color: rgba(0, 0, 0, 0.25); */
}

.weather-box .temp,
.weather-box .conditions {
  margin: 15px;
  padding: 10px 15px;
  background-color: rgba(255, 255, 255, 0.5);
}

.weather-box .temp {
  border-radius: 15px;
  color: yellow;
  font-weight: 800;
  font-size: 3.5rem;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.weather-box .conditions {
  /* background-color: rgba(255, 255, 255, 0.35); */
  border-radius: 15px;
  font-size: 1.5rem;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  /* margin: ; */
}

.weather-results-error {
  color: red;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.25);
}
</style>
