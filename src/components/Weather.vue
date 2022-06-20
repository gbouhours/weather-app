<template>
  <div class="container max-w-screen-xl p-4 mx-auto">
    <h1 class="mb-6 text-3xl text-center text-white">App météo avec Vue.js</h1>
    <div class="w-full p-4 mx-auto mb-12 bg-white border-2 rounded-lg md:w-3/5 border-cyan-800" v-if="weather_position">
      <p class="mb-3 font-sans text-base text-center text-stone-900">La météo à : <span class="text-xl">{{city}}</span>
      </p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Température :
        {{weather_position.main.temp.toFixed()}}°C</p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Température ressenti :
        {{weather_position.main.feels_like.toFixed()}}°C</p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Temps : {{weather_position.weather[0].description}}
      </p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Taux humidité : {{weather_position.main.humidity}}%
      </p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Vent :
        {{(weather_position.wind.speed*3600/1000).toFixed()}}km/h</p>
      <img :src="`http://openweathermap.org/img/wn/${weather_position.weather[0].icon}@2x.png`"
        :alt="`${weather_position.weather[0].description}`" class="mx-auto">
    </div>
    <div class="w-full mx-auto mb-12 md:w-3/5">
      <label for="position" class="block mb-2 text-lg font-medium text-white">Entrer le nom d'une ville</label>
      <input type="text" id="position"
        class="bg-secondary-50 border-2 border-cyan-800 text-blue-500 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5"
        v-model="request" @keypress.enter="getWeather">
    </div>
    <div class="w-full p-4 mx-auto mb-12 bg-white border-2 rounded-lg md:w-3/5 border-cyan-800" v-if="weather">
      <p class="mb-3 font-sans text-base text-center text-stone-900">La météo à : <span
          class="text-xl">{{weather.name}}</span></p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Température : {{weather.main.temp.toFixed()}}°C
      </p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Température ressenti :
        {{weather.main.feels_like.toFixed()}}°C</p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Temps : {{weather.weather[0].description}}</p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Taux humidité : {{weather.main.humidity}}%</p>
      <p class="mb-3 font-sans text-base text-center text-stone-900">Vent :
        {{(weather.wind.speed*3600/1000).toFixed()}}km/h</p>
      <img :src="`http://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`"
        :alt="`${weather.weather[0].description}`" class="mx-auto">

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