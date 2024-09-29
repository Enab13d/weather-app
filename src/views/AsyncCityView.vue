<template>
  <div></div>
</template>

<script setup>
import axios from "axios";
import { useRoute } from "vue-router";
const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${
        route.query.lat
      }&lon=${route.query.lng}&appid=${
        import.meta.env.VITE_WEATHER_API_KEY
      }&units=metric`
    );

    return weatherData;
  } catch (error) {
    console.log(error);
  }
};

const weatherData = await getWeatherData()
</script>
