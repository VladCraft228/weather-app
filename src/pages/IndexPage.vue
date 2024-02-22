<template>
  <q-page class="flex flex-center">
    <weather-data />
  </q-page>
</template>

<script>
import WeatherData from "src/components/WeatherData.vue";
import { defineComponent } from "vue";

export default defineComponent({
  components: { WeatherData },
  name: "IndexPage",
});
</script>
