<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        @input="getSearchResults"
        v-model="searchQuery"
        type="text"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66]"
        v-if="mapboxSearchResults"
      >
        <p v-if="searchError">Something went wrong, please try again</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">
          No results match Your query
        </p>
        <template v-else>
          <li
            v-for="result in mapboxSearchResults"
            :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)"
          >
            {{ result.properties.full_address }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback> 
          <CityCardSceleton/>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";
import CityCardSceleton from "@/components/CityCardSceleton.vue";
const router = useRouter();
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);
const serverError = ref(null);
const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/search/geocode/v6/forward?q=${
            searchQuery.value
          }&access_token=${import.meta.env.VITE_MAPBOX_API_TOKEN}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
      } catch (err) {
        if (err.code >= 500) {
          serverError.value = true;
          return;
        }
        searchError.value = true;
      }

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
const previewCity = (searchResult) => {
  const [city, state] = searchResult.properties.full_address.split(",");
  router.push({
    name: "cityView",
    params: { state: state.trim(), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>
