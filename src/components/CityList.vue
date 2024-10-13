<template>
  <ul>
    <li v-for="city in savedCities" :key="city.id">
      <CityCard :city="city"/>
    </li>
  </ul>
  <p v-if="!savedCities.length">
    No locations added. To start tracking a location, search in
    the field above.
  </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import CityCard from "./CityCard.vue";
const savedCities = ref([]);

const getCities = async() => {
  if (localStorage.getItem("savedCities")){
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    const requests = []
    savedCities.value.forEach(city => {
        requests.push(axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${
        city.coords.lat
      }&lon=${city.coords.lng}&appid=${
        import.meta.env.VITE_WEATHER_API_KEY
      }&units=metric`
    ))
    })
    const weatherData = await Promise.all(requests)
    weatherData.forEach((value, idx) => {
        savedCities.value[idx].weather = value.data
    })
}
};

await getCities()
</script>
