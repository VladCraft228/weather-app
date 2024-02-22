<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated :class="isActiveDarkMode ? 'bg-secondary' : 'white'">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
        <q-toolbar-title> Weather App </q-toolbar-title>
        <q-toggle
          v-model="isActiveDarkMode"
          @update:model-value="darkModeUpdate"
          icon="nightlight"
          unchecked-icon="light_mode"
          :color="isActiveDarkMode ? 'green-5' : 'white'"
          keep-color
        />
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header>Essential Links</q-item-label>
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted, watch } from "vue";
import EssentialLink from "components/EssentialLink.vue";

const linksList = [
  {
    title: "Quasar's Docs",
    caption: "quasar.dev",
    icon: "school",
    link: "https://quasar.dev",
  },
  {
    title: "Quasar's Github",
    caption: "github.com/quasarframework",
    icon: "bi-github",
    link: "https://github.com/quasarframework",
  },
  {
    title: "Weather App GitHub",
    caption: "github.com/quasarframework",
    icon: "bi-github",
    link: "https://github.com/VladCraft228/weather-app",
  },
];
export default defineComponent({
  name: "MainLayout",

  components: {
    EssentialLink,
  },
  setup() {
    const leftDrawerOpen = ref(false);
    const savedDarkMode = localStorage.getItem("darkMode");
    const isActiveDarkMode = ref(savedDarkMode === "true");

    onMounted(() => {
      setDarkMode(isActiveDarkMode.value);
    });

    watch(isActiveDarkMode, (val) => {
      setDarkMode(val);
    });

    function setDarkMode(val) {
      localStorage.setItem("darkMode", val);
      document.body.classList.toggle("body--dark", val);
      document.body.classList.toggle("body--light", !val);
    }

    function darkModeUpdate(value) {
      setDarkMode(value);
    }

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
      isActiveDarkMode,
      darkModeUpdate,
    };
  },
});
</script>
