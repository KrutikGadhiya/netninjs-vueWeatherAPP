<template>
  <div>
    <Suspense>
      <AyncCityView />
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AyncCityView from "../components/AyncCityView.vue";
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
</script>
