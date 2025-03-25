<template>
  <div class="container">
    <h1>Today's Weather in Poznan</h1>
    <WeatherCard />
  </div>
</template>

<script>
import WeatherCard from './components/WeatherCard.vue'

export default {
  name: 'App',
  components: {
    WeatherCard
  }
}
</script> 