<template>
  <div class="weather-card">
    <div class="loading" v-if="loading">Loading weather data...</div>
    
    <div v-if="weather && !loading" class="weather-info">
      <div class="weather-header">
        <div class="temperature">
          {{ Math.round(weather.main.temp) }}°C
        </div>
        <div class="weather-icon">
          <img :src="weatherIconUrl" alt="Weather icon">
        </div>
      </div>
      
      <div class="weather-details">
        <p><strong>Description:</strong> {{ capitalizeFirstLetter(weather.weather[0].description) }}</p>
        <p><strong>Feels like:</strong> {{ Math.round(weather.main.feels_like) }}°C</p>
        <p><strong>Humidity:</strong> {{ weather.main.humidity }}%</p>
        <p><strong>Wind:</strong> {{ Math.round(weather.wind.speed * 3.6) }} km/h</p>
      </div>
    </div>
    
    <div class="error-message" v-if="error">
      Sorry, we couldn't fetch the weather data. Please try again later.
    </div>
    
    <div class="updated-at" v-if="weather">
      <p>Last updated: {{ lastUpdated }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { ref, onMounted, computed } from 'vue'

export default {
  name: 'WeatherCard',
  setup() {
    // Just use your API key directly for now
    console.log(import.meta.env)
    const apiKey = import.meta.env.VITE_WEATHER_API_KEY // Put your actual working key back for now
    
    // Vite environment variables will be handled once configuration is fixed
    // We can re-implement this later
    
    const city = 'Poznan'
    
    const weather = ref(null)
    const loading = ref(true)
    const error = ref(false)
    const lastUpdated = ref('')
    
    const weatherIconUrl = computed(() => {
      if (!weather.value) return ''
      const iconCode = weather.value.weather[0].icon
      return `https://openweathermap.org/img/wn/${iconCode}@2x.png`
    })
    
    const fetchWeather = async () => {
      loading.value = true
      error.value = false
      
      try {
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather`, {
          params: {
            q: city,
            appid: apiKey,
            units: 'metric'
          }
        })
        
        weather.value = response.data
        lastUpdated.value = new Date().toLocaleTimeString()
      } catch (err) {
        console.error('Error fetching weather data:', err)
        error.value = true
      } finally {
        loading.value = false
      }
    }
    
    const capitalizeFirstLetter = (string) => {
      return string.charAt(0).toUpperCase() + string.slice(1)
    }
    
    onMounted(() => {
      fetchWeather()
    })
    
    return {
      weather,
      loading,
      error,
      lastUpdated,
      weatherIconUrl,
      capitalizeFirstLetter
    }
  }
}
</script> 