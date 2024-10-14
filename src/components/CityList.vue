<template>
  <ul>
    <li v-for="city in savedCities" :key="city.id" class="mb-2">
      <CityCard :city="city" @click="goToCityView(city)" />
    </li>
  </ul>
  <p v-if="!savedCities.length">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";
const savedCities = ref([]);

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${
            city.coords.lat
          }&lon=${city.coords.lng}&appid=${
            import.meta.env.VITE_WEATHER_API_KEY
          }&units=metric`
        )
      );
    });
    // Flicker delay
    await new Promise((res) => setTimeout(res, 1000))
    const weatherData = await Promise.all(requests);
    weatherData.forEach((value, idx) => {
      savedCities.value[idx].weather = value.data;
    });
  }
};

await getCities();
const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: { id: city.id, lat: city.coords.lat, lng: city.coords.lng },
  });
};
</script>
