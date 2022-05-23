<template>
  <weather-app></weather-app>
</template>

<script>
import WeatherApp from './components/Weather.vue'

export default {
  name: 'App',
  components: {
    WeatherApp,
  },
  data() {
    return {
    }
  }
}
</script>
<style>
body {
  @apply bg-gradient-to-r;
  @apply from-cyan-400;
  @apply to-blue-400;
}
</style>