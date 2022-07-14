<template>
  <div class="container max-w-screen-xl p-4 mx-auto">
    <h1 class="mb-6 text-3xl text-center text-white">Weather App</h1>
    <div class="w-full mx-auto mb-12 md:w-3/5">
      <label for="position" class="block mb-2 text-lg font-medium text-white">Search city</label>
      <input type="text" placeholder="city" id="position"
        class="bg-secondary-50 border-2 border-cyan-800 text-blue-500 text-sm uppercase font-bold rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
        v-model="request" @keypress.enter="getWeather">
    </div>
    <div class="w-full p-4 mx-auto mb-12 bg-white border-2 rounded-lg md:w-3/5 border-cyan-800" v-if="weather_forecast">
      <p class="mb-3 font-sans text-base text-center capitalize text-cyan-700">Current weather : <span
          class="text-xl">{{ request_city }}</span></p>
      <div v-if="weather" class="flex gap-4 p-4 mb-3 border-2 border-cyan-800">
        <WeatherCurrentCard :temp=weather.main.temp.toFixed() :feelsLike=weather.main.feels_like.toFixed()
          :humidity=weather.main.humidity :wind=weather.wind.speed :nameIcon=weather.weather[0].icon
          :descriptionIcon=weather.weather[0].description />
      </div>
      <p class="mb-3 text-lg font-medium text-cyan-800">Forecast</p>
      <div v-for="(datas, index) in weather_forecast" :key="index" class="mb-8">
        <button @click="datas.isHidden = !datas.isHidden"
          class="w-full p-4 my-2 text-lg uppercase transition-colors border-2 text-cyan-700 border-cyan-800 hover:text-white hover:bg-cyan-800">{{
          Object.keys(datas[0]).toString()
          }}</button>
        <div class="flex flex-wrap justify-center gap-4">
          <div v-for="(items, index) in datas" :key="index" v-show="datas.isHidden" class="w-full lg:w-2/5">
            <div v-for="(item, index) in items" :key="index" class="p-4 mb-6 border-2 border-cyan-800">
              <WeatherCard :hour=item.dt :temp=item.main.temp.toFixed() :feelsLike=item.main.feels_like.toFixed()
                :humidity=item.main.humidity :wind=item.wind.speed :nameIcon=item.weather[0].icon
                :descriptionIcon=item.weather[0].description />
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="w-full p-4 mx-auto mb-12 bg-white border-2 rounded-lg md:w-3/5 border-cyan-800"
      v-if="weather_position_forecast" v-show="!weather">
      <p class="mb-3 font-sans text-base text-center text-cyan-700">Current weather in your location : <span
          class="text-xl">{{ city }}</span>
      </p>
      <div v-if="weather_position" class="flex gap-4 p-4 mb-3 border-2 border-cyan-800">
        <WeatherCurrentCard :temp=weather_position.main.temp.toFixed()
          :feelsLike=weather_position.main.feels_like.toFixed() :humidity=weather_position.main.humidity
          :wind=weather_position.wind.speed :nameIcon=weather_position.weather[0].icon
          :descriptionIcon=weather_position.weather[0].description />
      </div>
      <p class="mb-3 text-lg font-medium text-cyan-800">Forecast</p>
      <div v-for="(datas, index) in weather_position_forecast" :key="index" class="mb-8">
        <button @click="datas.isHidden = !datas.isHidden"
          class="w-full p-4 my-2 text-lg uppercase transition-colors border-2 text-cyan-700 border-cyan-800 hover:text-white hover:bg-cyan-800">{{
          Object.keys(datas[0]).toString() }}</button>
        <div class="flex flex-wrap justify-center gap-4">
          <div v-for="(items, index) in datas" :key="index" v-show="datas.isHidden" class="w-full lg:w-2/5">
            <div v-for="(item, index) in items" :key="index" class="p-4 border-2 border-cyan-800">
              <WeatherCard :hour=item.dt :temp=item.main.temp.toFixed() :feelsLike=item.main.feels_like.toFixed()
                :humidity=item.main.humidity :wind=item.wind.speed :nameIcon=item.weather[0].icon
                :descriptionIcon=item.weather[0].description />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import WeatherCurrentCard from './WeatherCurrentCard.vue'
import WeatherCard from './WeatherCard.vue'
export default {
  components: {
    WeatherCurrentCard,
    WeatherCard,
  },
    name: 'WeatherApp',
    data() {
      return {
        request: '',
        request_city: '',
        lat: '',
        lon: '',
        weather: undefined,
        weather_position: undefined,
        weather_forecast: undefined,
        weather_position_forecast: undefined,
        city: '',
        api_key: process.env.VUE_APP_OPENWEATHERMAP_API_KEY,
        api_url_forecast: 'https://api.openweathermap.org/data/2.5/forecast?',
        api_url_weather: 'https://api.openweathermap.org/data/2.5/weather?',
      }
    },
    methods: {
      getWeather() {
        const currentDay = new Date().getDate();
        let date = '';
        let result = [];
        axios
        .get(`${this.api_url_forecast}q=${this.request}&units=metric&appid=${this.api_key}&lang=fr`)
          .then(response => {
            for (const key in response.data.list) {
              if (currentDay == new Date(response.data.list[key].dt * 1000).toLocaleString('en-US', { day: 'numeric' })) {
                result.push({ ['today']: response.data.list[key] });
              } else if ((currentDay + 1) == new Date(response.data.list[key].dt * 1000).toLocaleString('en-US', { day: 'numeric' })) {
                result.push({ ['tomorrow']: response.data.list[key] });
              } else {
                date = new Date(response.data.list[key].dt * 1000).toLocaleString("en-US", { weekday: "long" }) + ', ' + new Date(response.data.list[key].dt
                  * 1000).toLocaleString("en-US", { day: "numeric" }) + '. ' + new Date(response.data.list[key].dt * 1000).toLocaleString("en-US", { month: "long" });
                result.push({ [date]: response.data.list[key] });
              }
            }
            this.weather_forecast =
              // drop the keys of the object created by the reduce
              Object.values(
                // for each object in the array
                result.reduce((acc, el) => {
                  // for each keys in the object
                  Object.keys(el).forEach(key => {
                    // add the object to the group of objects with this key
                    acc[key] = acc[key] || []
                    acc[key].push(el)
                  })
                  return acc
                }, {})
              )
          })
        axios
          .get(`${this.api_url_weather}q=${this.request}&units=metric&appid=${this.api_key}&lang=fr`)
          .then(response => {
            return this.weather = response.data
          })
        this.request_city = this.request
        this.request = ''
      }
    },
    created() {
      const success = (position) => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        const currentDay = new Date().getDate();
        let date = '';
        let result = [];

        axios
        .get(`https://api.openweathermap.org/geo/1.0/reverse?lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.api_key}&lang=fr`)
        .then(response => {
          this.city = response.data[0].name;
        })

        axios
        .get(`${this.api_url_forecast}lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.api_key}&lang=fr`)
        .then(response => {
          for (const key in response.data.list) {
            if (currentDay == new Date(response.data.list[key].dt *1000).toLocaleString('en-US', { day: 'numeric' })) {
              result.push({ ['today']: response.data.list[key] });
            } else if((currentDay +1) == new Date(response.data.list[key].dt * 1000).toLocaleString('en-US', { day: 'numeric' })) {
              result.push({ ['tomorrow']: response.data.list[key] });
            } else {
              date = new Date(response.data.list[key].dt * 1000).toLocaleString("en-US", { weekday: "long" })+', ' + new Date(response.data.list[key].dt
                * 1000).toLocaleString("en-US", { day: "numeric" }) + '. ' + new Date(response.data.list[key].dt * 1000).toLocaleString("en-US", {month: "long"});
              result.push({ [date]: response.data.list[key] });
            }
          }
          this.weather_position_forecast =
            // drop the keys of the object created by the reduce
            Object.values(
              // for each object in the array
              result.reduce((acc, el) => {
                // for each keys in the object
                Object.keys(el).forEach(key => {
                  // add the object to the group of objects with this key
                  acc[key] = acc[key] || []
                  acc[key].push(el)
                })
                return acc
              }, {})
            )
        })

        axios
          .get(`${this.api_url_weather}lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.api_key}&lang=fr`)
          .then(response => {
            return this.weather_position = response.data
          })
      }

      const error = (error) => {
        console.log(error);
      }

      navigator.geolocation.getCurrentPosition(success, error)
    },
  }
</script>
