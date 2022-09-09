<template>
  <div>
    <Suspense>
      <AyncCityView />
      <template #fallback>
        <p>Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AyncCityView from "./AyncCityView.vue";
</script>
