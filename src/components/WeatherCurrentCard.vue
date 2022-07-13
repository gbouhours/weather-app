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
  <img 
    :src="`http://openweathermap.org/img/wn/${props.nameIcon}@2x.png`" 
    :alt="`${props.descriptionIcon}`"
    :title="`${props.descriptionIcon}`"
    class="w-24 h-full">
  <div>
    <p class="mb-3 font-sans text-2xl text-left text-cyan-700">
      Temperature: {{ props.temp }}°C
    </p>
    <p class="mb-3 font-sans text-2xl text-left text-cyan-700">
      Temperature feels like : {{ props.feelsLike }}°C
    </p>
    <p class="mb-3 font-sans text-2xl text-left text-cyan-700">
      Humidity : {{ props.humidity }}%
    </p>
    <p class="mb-3 font-sans text-2xl text-left text-cyan-700">
      Wind : {{ (props.wind * 3600 / 1000).toFixed() }}km/h
    </p>
  </div>
</template>
