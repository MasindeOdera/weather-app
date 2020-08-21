<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ?
    'warm' : '' ">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Search..." 
          v-model="query"
          @keypress="fetchWeather"
          autocomplete="off"
        />
      </div>

      <div class="error" v-if="typeof error != 'undefined'">{{ error }}</div>
      <div class="suggestion" v-if="error.length > 0">(Was there a typo?)</div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="report">
            <div class="icon">
              <img :src="icon" alt="Weather Icon" width="90" height="90">
            </div>
            <div class="feels">
              <span>Max: {{ Math.round(weather.main.temp_max) }}°c</span>
              <span>Min: {{ Math.round(weather.main.temp_min) }}°c</span>
            </div>
          </div>
          <div class="hint">Searching for another <b>{{ weather.name }}</b>? Add a comma followed by the country code.</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  metaInfo() {
        return { 
            title: "Weather App - Insert a location to discover the forecast.",
            meta: [
                { name: 'description', content:  'Weather App was developed with Vue.js using the OpenWeather API. Input a location to get real-time forecast.'},
                { property: 'og:title', content: "Weather App - Insert a location to discover the forecast."},
                { property: 'og:image', content: './assets/weather.png'},
                { property: 'og:site_name', content: 'WeatherApp'},
                {property: 'og:type', content: 'website'},    
                {name: 'robots', content: 'index,follow'} 
            ]
        }
    },
  data() {
    return {
      api_key: '2dc9abfe077c677cb92a5afcea396764',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      error:'',
      icon:'',
      iconStart: 'http://openweathermap.org/img/wn/',
      iconEnd: '@2x.png'
    }
  },
  methods: {
    fetchWeather(e) {
      if(e.key == "Enter"){
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
          e.target.blur();
      }
    },
    setResults(results) {
      //Assign fetched results to weather.
      this.weather = results;

      //If the fetch returns data, then create the url to get the icon.
      this.icon = results.cod !== '404' ? this.iconStart + results.weather[0].icon + this.iconEnd : '';

      //set the query back to empty so that it automatically clears search input.
      this.query = '';

      //If location is not found, assigns message, otherwise it remains empty. 
      this.error = results.message ? results.message : '';
      // console.log(results);
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", 
      "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", 
      "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day}, ${date} ${month} ${year}`;
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

div#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

div#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  /*The following are all just resets */
  appearance: none;
  border: none;
  outline:none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255,0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255,7.5);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0,0,0,0.25);
}

.location-box .date, .feels, .hint {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255,0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0,0,0,0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0,0,0,0.25);;
}

div.report {
  width: 50%;
  margin: auto;
  display: inline-flex;
  vertical-align: middle;
  justify-content: center;
}

div.feels {
  vertical-align: middle;
  margin-top: 1.4rem;
  text-align: left;
}

div.feels span {
  display: block;
  width: 130px;
}

.error {
  text-align: center;
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0,0,0,0.25);;
}

.suggestion {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
</style>
