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
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const router = useRouter();
const searchQuery = ref("");
const queryTimeout = ref(null);
// const searchReasults = ref(null);
const searchReasults = ref([
  {
    description: "Halol, Gujarat, India",
    matched_substrings: [
      {
        length: 4,
        offset: 0,
      },
    ],
    place_id: "ChIJWx_I8_B_YDkRZbk4EJ-UUvQ",
    reference: "ChIJWx_I8_B_YDkRZbk4EJ-UUvQ",
    structured_formatting: {
      main_text: "Halol",
      main_text_matched_substrings: [
        {
          length: 4,
          offset: 0,
        },
      ],
      secondary_text: "Gujarat, India",
    },
    terms: [
      {
        offset: 0,
        value: "Halol",
      },
      {
        offset: 7,
        value: "Gujarat",
      },
      {
        offset: 16,
        value: "India",
      },
    ],
    types: ["locality", "political", "geocode"],
  },
  {
    description: "Haloli, Maharashtra, India",
    matched_substrings: [
      {
        length: 4,
        offset: 0,
      },
    ],
    place_id: "ChIJa8ROUuMP5zsR72kPv1Io_jI",
    reference: "ChIJa8ROUuMP5zsR72kPv1Io_jI",
    structured_formatting: {
      main_text: "Haloli",
      main_text_matched_substrings: [
        {
          length: 4,
          offset: 0,
        },
      ],
      secondary_text: "Maharashtra, India",
    },
    terms: [
      {
        offset: 0,
        value: "Haloli",
      },
      {
        offset: 8,
        value: "Maharashtra",
      },
      {
        offset: 21,
        value: "India",
      },
    ],
    types: ["locality", "political", "geocode"],
  },
  {
    description: "Halogy, Hungary",
    matched_substrings: [
      {
        length: 4,
        offset: 0,
      },
    ],
    place_id: "ChIJo6OZhRfRbkcRDme040tI1Kc",
    reference: "ChIJo6OZhRfRbkcRDme040tI1Kc",
    structured_formatting: {
      main_text: "Halogy",
      main_text_matched_substrings: [
        {
          length: 4,
          offset: 0,
        },
      ],
      secondary_text: "Hungary",
    },
    terms: [
      {
        offset: 0,
        value: "Halogy",
      },
      {
        offset: 8,
        value: "Hungary",
      },
    ],
    types: ["locality", "political", "geocode"],
  },
  {
    description: "Halondi, Maharashtra, India",
    matched_substrings: [
      {
        length: 4,
        offset: 0,
      },
    ],
    place_id: "ChIJYWoEUnoBwTsR_PDG1VJ6tXg",
    reference: "ChIJYWoEUnoBwTsR_PDG1VJ6tXg",
    structured_formatting: {
      main_text: "Halondi",
      main_text_matched_substrings: [
        {
          length: 4,
          offset: 0,
        },
      ],
      secondary_text: "Maharashtra, India",
    },
    terms: [
      {
        offset: 0,
        value: "Halondi",
      },
      {
        offset: 9,
        value: "Maharashtra",
      },
      {
        offset: 22,
        value: "India",
      },
    ],
    types: ["locality", "political", "geocode"],
  },
  {
    description: "Halong, Balangan Regency, South Kalimantan, Indonesia",
    matched_substrings: [
      {
        length: 4,
        offset: 0,
      },
    ],
    place_id: "ChIJAeUpbOcB8C0Rg_VKvRM9f8o",
    reference: "ChIJAeUpbOcB8C0Rg_VKvRM9f8o",
    structured_formatting: {
      main_text: "Halong",
      main_text_matched_substrings: [
        {
          length: 4,
          offset: 0,
        },
      ],
      secondary_text: "Balangan Regency, South Kalimantan, Indonesia",
    },
    terms: [
      {
        offset: 0,
        value: "Halong",
      },
      {
        offset: 8,
        value: "Balangan Regency",
      },
      {
        offset: 26,
        value: "South Kalimantan",
      },
      {
        offset: 44,
        value: "Indonesia",
      },
    ],
    types: ["administrative_area_level_3", "political", "geocode"],
  },
]);
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
    return;
    if (searchQuery.value !== "") {
      try {
        const res = await axios.get(
          "https://google-maps28.p.rapidapi.com/maps/api/place/autocomplete/json?input=halo&language=en&types=(cities)",
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
        console.log(searchReasults.value);
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
