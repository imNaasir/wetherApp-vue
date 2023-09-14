<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Search..." v-model="query" @keypress="fetchWeather" />
      </div>

      <div class="weather-wrap">
        <div class="location-box">
          <div class="location">{{ cities }}</div>
          <div class="date">{{ date_time }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ temperature }}°C</div>
          <div class="weather">{{ weather_text }}</div>
          <div class="detail">
            <div style="display: flex; column-gap: 8px; align-items: center;">
              <img src="./assets/images/humidity.png" alt="" style="width: 50px; height: 40px; opacity: 0.5;">
              <div>
                <div class="details">{{ humidity }}%</div>
                <small class="details" style="font-size: 15px; ">Humidity</small>
              </div>
            </div>
            <div style="display: flex; column-gap: 8px; align-items: center; ">
              <img src="./assets/images/wind.png" alt="" style="width: 50px; height: 40px; opacity: 0.5;">
              <div style="display: flex;  flex-direction: column; ">
                <div class="details">{{ speed }}km/h</div>
                <small class="details" style="font-size: 15px; text-align: start; ">Wind Speed</small>
              </div>
            </div>
            <!-- <div class="details">{{ speed }}km/h</div> -->
          </div>
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
      api_key: '494a1b4f5973f35f71dcf349e65bd406',
      // url_base: 'http://dataservice.accuweather.com/locations/v1/cities/autocomplete',
      url_base: 'https://api.openweathermap.org/data/2.5/weather?units=metric&q=',
      condition_url: 'http://dataservice.accuweather.com/currentconditions/v1/',
      query: '',
      weather: {},
      cities: 'New York',
      country: 'country',
      temperature: '21°C',
      key: '301348',
      temp_Resul: '',
      weather_text: 'overcast clouds',
      date_time: 'Thu, 14 Sep 2023',
      humidity: '77%',
      speed: '10 km/h',

    }
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}${this.query.trim().toLocaleLowerCase()}&appid=${this.api_key}`)
          .then(res => {
            const data = res.json();
            console.log("the data:==", data);
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
      console.log("the weather:==", this.weather);
      // this.weather.forEach(cities => {
      this.cities = this.weather.name
      this.country = this.weather.sys.country
      this.temperature = Math.round(this.weather.main.temp)
      this.weather_text = this.weather.weather[0].description
      this.humidity = this.weather.main.humidity
      this.speed = this.weather.wind.speed
      let now = new Date(this.weather.dt * 1000 - (this.weather.timezone * 1000));
      const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
      const dateNames = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
      let currentTime = ` ${dateNames[now.getDay() - 1]},  ${now.getDate()} ${monthNames[now.getMonth()]} ${now.getFullYear()} `
      console.log(currentTime)
      this.date_time = currentTime

      localStorage.setItem("data", JSON.stringify({ city: this.weather.name, country: this.weather.sys.country, temp: Math.round(this.weather.main.temp), weather: this.weather.weather[0].description, humidity: this.weather.main.humidity, speed: this.weather.wind.speed, dateTime: currentTime }))
      // });
    },


  },
  mounted() {
    if (localStorage.getItem("data")?.length > 0) {
      let data = JSON.parse(localStorage.getItem("data"))
      // console.log(JSON.parse(data));
      this.cities = data.city
      this.country = data.country
      this.temperature = data.temp
      this.weather_text = data.weather
      this.humidity = data.humidity
      this.speed = data.speed
      this.date_time = data.dateTime

    };
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
  width: 100vw;
  overflow: hidden;
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
  font-size: 48px;
  font-weight: 700;
  text-align: center;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  margin-top: 60px;
  color: #FFF;
  font-style: italic;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}


.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 70px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

@media only screen and (min-width: 768px) {
  .weather-box .temp {
    font-size: 102px;
  }
}

.weather-box .weather {
  color: #FFF;
  font-style: italic;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.detail {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  justify-content: space-between;
  margin: 80px 40px;
}

.detail .details {
  color: #FFF;
  opacity: 0.8;
  font-size: 28px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>


//fetch(`?apikey=&q=`)