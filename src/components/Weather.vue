<template>
  <div class="container mx-auto max-w-screen-xl p-4">
    <h1 class="text-3xl text-white text-center mb-6">App météo avec Vue.js</h1>
      <div class="w-full md:w-3/5 mx-auto border-2 border-cyan-800 rounded-lg bg-white p-4 mb-12" v-if="weather_position">
        <p class="text-center text-base font-sans text-stone-900 mb-3">La météo à : <span class="text-xl">{{city}}</span></p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Température : {{weather_position.main.temp.toFixed()}}°C</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Température ressenti : {{weather_position.main.feels_like.toFixed()}}°C</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Temps : {{weather_position.weather[0].description}}</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Taux humidité : {{weather_position.main.humidity}}%</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Vent : {{(weather_position.wind.speed*3600/1000).toFixed()}}km/h</p>
      </div>
      <div class="mb-12 w-full md:w-3/5 mx-auto">
        <label for="position" class="block mb-2 text-lg font-medium text-white">Entrer le nom d'une ville</label>
        <input 
          type="text" 
          id="position" 
          class="bg-secondary-50 border-2 border-cyan-800 text-blue-500 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
          v-model="request"
          @keypress.enter="getWeather"
          >
      </div>
      <div class="w-full md:w-3/5  mx-auto border-2 border-cyan-800 rounded-lg bg-white p-4 mb-12" v-if="weather">
        <p class="text-center text-base font-sans text-stone-900 mb-3">La météo à : <span class="text-xl">{{weather.name}}</span></p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Température : {{weather.main.temp.toFixed()}}°C</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Température ressenti : {{weather.main.feels_like.toFixed()}}°C</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Temps : {{weather.weather[0].description}}</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Taux humidité : {{weather.main.humidity}}%</p>
        <p class="text-center text-base font-sans text-stone-900 mb-3">Vent : {{(weather.wind.speed*3600/1000).toFixed()}}km/h</p>

      </div>
  </div>
</template>
<script>
import axios from 'axios'

  export default {
    name: 'WeatherApp',
    data() {
      return {
        request: '',
        lat: '',
        lon: '',
        weather: undefined,
        weather_position: undefined,
        city: '',
        api_key: process.env.VUE_APP_OPENWEATHERMAP_API_KEY,
        api_url: 'https://api.openweathermap.org/data/2.5/weather?',
      }
    },
    methods: {
      getWeather() {
        axios
        .get(`${this.api_url}q=${this.request}&units=metric&appid=${this.api_key}&lang=fr`)
        .then(response => {
          this.weather = response.data;
        })
        this.request = ''
      }
    },
    created() {
      const success = (position) => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        axios
        .get(`http://api.openweathermap.org/geo/1.0/reverse?lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.api_key}&lang=fr`)
        .then(response => {
          this.city = response.data[0].name;
        })

        axios
        .get(`${this.api_url}lat=${this.lat}&lon=${this.lon}&units=metric&appid=${this.api_key}&lang=fr`)
        .then(response => {
          this.weather_position = response.data;
        })
      }

      const error = (error) => {
        console.log(error);
      }

      navigator.geolocation.getCurrentPosition(success, error)
    },
  }
</script>
<style></style>