<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="searchReasults"
      >
        <p v-if="searchError">Sorry, Something went wrong.</p>
        <p v-if="!serverError && searchReasults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResult in searchReasults"
            :key="searchResult.place_id"
            class="py-2 mx-1 px-2 cursor-pointer hover:bg-slate-500"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.description }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();
const searchQuery = ref("");
const queryTimeout = ref(null);
// const searchReasults = ref(null);
const searchReasults = ref(null);
const searchError = ref(null);

const previewCity = async (searchResult) => {
  console.log(searchResult);
  const [city, state] = searchResult.description.split(",");

  try {
    // const res = await axios.get(
    //   `https://google-maps28.p.rapidapi.com/maps/api/geocode/json?place_id=${searchResult.place_id}&language=en`,
    //   {
    //     headers: {
    //       "X-RapidAPI-Key":
    //         "68a4bcd44cmshfd397ec827c7457p1f5ee7jsn95695c8fd1a6",
    //       "X-RapidAPI-Host": "google-maps28.p.rapidapi.com",
    //     },
    //   }
    // );
    // console.log(res.data);
    // console.log(res.data.results[0]);

    // const { lat, lng } = res.data.results[0].geometry.location;
    const { lat, lng } = {
      lat: 22.5045713,
      lng: 73.4713666,
    };

    router.push({
      name: "cityView",
      params: {
        city: city.replaceAll(" ", ""),
        state: state.replaceAll(" ", ""),
      },
      query: {
        lat,
        lng,
        preview: true,
      },
    });
  } catch (error) {
    console.log(error);
    searchError.value = true;
  }
};

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const res = await axios.get(
          `https://google-maps28.p.rapidapi.com/maps/api/place/autocomplete/json?input=${searchQuery.value}&language=en&types=(cities)`,
          {
            headers: {
              "X-RapidAPI-Key":
                "68a4bcd44cmshfd397ec827c7457p1f5ee7jsn95695c8fd1a6",
              "X-RapidAPI-Host": "google-maps28.p.rapidapi.com",
            },
          }
        );
        console.log(res.data);
        searchReasults.value = res.data.predictions;
        // console.log(searchReasults.value);
      } catch (error) {
        console.log(error);
        searchError.value = true;
      }

      return;
    }
    searchReasults.value = null;
  }, 1000);
};
</script>

<!-- 
  const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        searchReasults.value = result.data.features;
      } catch {
        searchError.value = true;
      }
      return;
    }
    searchReasults.value = null;
  }, 300);
};
 -->
