<template>
  <div class="weather" :class="weatherClass">
    <div class="container">
      <div class="card weather-form">
        <input type="text" class="weather-form_input" v-model="searchQuery" @keyup.enter="weatherSearch"
               placeholder="Enter city">
        <button class="weather-form_btn" @click="weatherSearch">Search</button>
      </div>

      <div class="card weather-loader" v-if="loading">Loading...</div>

      <div class="weather-info" v-show="!error && location && temperature !==0 && description">

        <div class="card" v-if="error">Error</div>

        <div class="weather-info_text">
          <p class="card">{{ location }}</p>
          <p class="card">{{ temperature }} °C</p>
          <p class="card">{{ description }}</p>
        </div>
      </div>
    </div>
    <div class="weather-bg">
      <div>
        <img class="weather-bg_img bg" src="./assets/background.jpg" alt="App background">
        <img class="weather-bg_img cloudy" src="./assets/cloudly.jpg" alt="App Cloudly">
        <img class="weather-bg_img overcast" src="./assets/overcast.jpg" alt="Overcast">
        <img class="weather-bg_img sunny" src="./assets/sunny.jpg" alt="Sunny">
      </div>
    </div>
  </div>
</template>

<script setup>

import {computed, ref} from "vue";

const location = ref('')
const temperature = ref(0)
const description = ref('')
const loading = ref(false)
const error = ref(false)
const searchQuery = ref('')

function weatherSearch() {
  loading.value = true
  error.value = false
  fetch(`https://api.weatherapi.com/v1/current.json?key=a1fec8e86b39450186d81443242211&q=${searchQuery.value}`)
      .then(response => response.json())
      .then(data => {
        loading.value = false
        location.value = data.location.name;
        temperature.value = data.current.temp_c;
        description.value = data.current.condition.text
        resetSearchQuery()
      })
      .catch(error => {
        loading.value = false
        error.value = true
        console.log(error)
      })
}

function resetSearchQuery() {
  searchQuery.value = ''
}

const weatherClass = computed(() => {
  if (description.value.includes('Sunny')) {
    return 'sunny'
  } else if (description.value.includes('Overcast')) {
    return 'overcast'
  } else if (description.value.includes('Partly cloudy')) {
    return 'cloudy'
  } else {
    return ''
  }
})


</script>