<script setup>
const props = defineProps({
  nameIcon: {
    type: String,
    required: true,
    default: '',
  },
  descriptionIcon: {
    type: String,
    required: true,
    default: '',
  },
  hour: {
    type: Number,
    required: true,
    default: null,
  },
  temp: {
    type: Number,
    required: true,
    default: null,
  },
  feelsLike: {
    type: Number,
    required: true,
    default: null,
  },
  humidity: {
    type: Number,
    required: true,
    default: null,
  },
  wind: {
    type: Number,
    required: true,
    default: null,
  },
});
</script>

<template>
  <p class="mb-3 font-sans text-2xl text-center text-cyan-700">
    {{ new Date(props.hour * 1000).toLocaleString("en-US", { hour: "numeric", hour12: false }) }}:00
  </p>
  <img 
    :src="`http://openweathermap.org/img/wn/${props.nameIcon}@2x.png`" 
    :alt="`${props.descriptionIcon}`" 
    :title="`${props.descriptionIcon}`"
    class="w-24 h-full mx-auto">
  <p class="mb-3 font-sans text-2xl text-center text-cyan-700">
    Temperature: {{ props.temp }}°C
  </p>
  <p class="mb-3 font-sans text-2xl text-center text-cyan-700">
    Temperature feels like : {{ props.feelsLike }}°C
  </p>
  <p class="mb-3 font-sans text-2xl text-center text-cyan-700">
    Humidity : {{ props.humidity }}%
  </p>
  <p class="mb-3 font-sans text-2xl text-center text-cyan-700">
    Wind : {{ (props.wind * 3600 / 1000).toFixed() }}km/h
  </p>
</template>
