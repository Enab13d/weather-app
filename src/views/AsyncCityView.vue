<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      You are currently previewing this city, click the "+" icon to start
      tracking this city.
    </div>
    <!-- Weather overview -->
    <div class="flex flex-col flex-1 items-center py-12 text-white">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.dt).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.dt).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ `${Math.round(weatherData.main.temp)}&deg` }}
      </p>
      <p>Feels like {{ `${Math.round(weatherData.main.feels_like)}&deg` }}</p>
      <p class="capitalize">
        {{ weatherData.weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <hr class="border-white border-opacity-10 border w-full">

  </div>
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


    return weatherData.data;
  } catch (error) {
    console.log(error);
  }
};

const weatherData = await getWeatherData();
</script>
