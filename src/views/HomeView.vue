<template>
  <main v-if="!loading">
    <DataTitle :text="title" :dataDate="dataDate" />
    <DataBoxes :status="status" />
    <CountrySelect :countries="countries" @get-country="getCountryData" />
    <button
      @click="clearCountryData"
      v-if="status.Country"
      class="bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white mt-5 py-2 px-4 border border-blue-500 hover:border-transparent rounded"
    >
      Clear Country
    </button>
  </main>
  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching Data</div>
    <img :src="loadingImage" class="w-24 m-auto" alt="" />
  </main>
</template>
<script>
import DataTitle from "@/components/DataTitle.vue";
import DataBoxes from "@/components/DataBoxes.vue";
import CountrySelect from "@/components/CountrySelect.vue";
import { ref } from "vue";
export default {
  name: "HomeView",
  components: { DataTitle, DataBoxes, CountrySelect, CountrySelect },
  setup() {
    const loading = ref(true);
    const title = ref("Global");
    const dataDate = ref("");
    const status = ref({});
    const countries = ref([]);

    const fetchCovidData = async () => {
      const res = await fetch("https://api.covid19api.com/summary");
      return await res.json();
    };
    const getCountryData = (country) => {
      status.value = country;
      title.value = country.Country;
    };
    const clearCountryData = async () => {
      loading.value = true;
      const data = await fetchCovidData();
      title.value = "Global";
      status.value = data.Global;
      loading.value = false;
    };
    const baseSetup = async () => {
      const data = await fetchCovidData();
      console.log(data);
      dataDate.value = data.Date;
      status.value = data.Global;
      countries.value = data.Countries;
      loading.value = false;
    };
    baseSetup();
    return {
      loading,
      title,
      dataDate,
      status,
      countries,
      getCountryData,
      clearCountryData
    };
  },
};
</script>
