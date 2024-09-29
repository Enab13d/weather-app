<template>
  <div>
    <Suspense
      ><AsyncCityView />
      <template #fallback>
        <p>Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import { Suspense } from "vue";
import AsyncCityView from "./AsyncCityView.vue";
</script>
