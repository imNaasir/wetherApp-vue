<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Search..." v-model="query" @keypress="fetchWeather" />
      </div>

      <div class="weather-wrap">
        <div class="location-box">
          <div class="location">{{ cities }}, {{ country }}</div>
          <div class="date">{{ date_time }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ temperature }}C</div>
          <div class="weather">{{ weather_text }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data() {
    return {
      api_key: 'xxIX9PSzKBh5II5QAJATVEboIh7wjoFJ',
      url_base: 'http://dataservice.accuweather.com/locations/v1/cities/autocomplete',
      condition_url: 'http://dataservice.accuweather.com/currentconditions/v1/',
      query: '',
      weather: {},
      cities: 'city',
      country: 'country',
      temperature: '',
      key: '301348',
      temp_Resul: '',
      weather_text: '',
      date_time: '',

    }
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}?apikey=${this.api_key}&q=${this.query.trim().toLocaleLowerCase()}`)
          .then(res => {
            const data = res.json();
            // console.log(data);
            return data;
          }).then(this.setResults);

        fetch(`http://dataservice.accuweather.com/currentconditions/v1/${this.key}/?apikey=${this.api_key}`)
          .then(res => {
            const data = res.json();
            // console.log(data);
            return data;
          }).then(this.setTemp);

        this.query = ''
      }

    },
    setResults(results) {
      this.weather = results;
      // console.log(this.weather);
      this.weather.forEach(cities => {
        this.cities = cities.LocalizedName
        this.country = cities.Country.LocalizedName
        this.key = cities.Key
        console.log(this.key);
        // localStorage.setItem
        localStorage.setItem("data", JSON.stringify({ city: cities.LocalizedName, country: cities.Country.LocalizedName, key: cities.Key }))
      });
    },
    setTemp(results) {
      this.temp_Resul = results;
      console.log(this.temp_Resul); //Temperature
      this.temp_Resul.forEach(temperature => {
        this.temperature = temperature.Temperature.Metric.Value
        this.weather_text = temperature.WeatherText
        this.date_time = temperature.LocalObservationDateTime
        console.log(this.date_time);

        localStorage.setItem("seconData", JSON.stringify({ temperature: temperature.Temperature.Metric.Value, wetherText: temperature.WeatherText, date_time: temperature.LocalObservationDateTime }))
      });
    },


  },
  mounted() {
    if (localStorage.getItem("data")?.length > 0) {
      let data = JSON.parse(localStorage.getItem("data"))
      // console.log(JSON.parse(data));
      this.cities =  data.city
      this.country =  data.country
      this.key = data.key
      
    };
    if (localStorage.getItem("seconData")?.length > 0) {
      let data = JSON.parse(localStorage.getItem("seconData"))
        this.temperature = data.temperature
        this.weather_text = data.wetherText
        this.date_time = data.date_time
    }
  },

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

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

/* #app.warm {
  background-image: url('./assets/warm-bg.jpg');
} */
main {
  min-height: 100vh;
  padding: 25px;
  background-image: url('https://img.freepik.com/premium-photo/background_889056-16814.jpg');
  background-position: left bottom;
  background-size: 100% 100%;
  background-repeat: no-repeat;
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
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
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
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>