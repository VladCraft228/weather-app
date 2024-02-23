<template>
  <!-- Основний макет з інтерфейсом Quasar -->
  <q-layout view="lHh Lpr lFf">
    <!-- Верхній блок з заголовком та перемикачем теми -->
    <q-header elevated :class="isActiveDarkMode ? 'bg-secondary' : 'white'">
      <q-toolbar>
        <!-- Кнопка меню -->
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
        <!-- Заголовок -->
        <q-toolbar-title> Weather App </q-toolbar-title>
        <!-- Перемикач теми -->
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

    <!-- Висувне меню з посиланнями -->
    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header>Essential Links</q-item-label>
        <!-- Компонент EssentialLink для кожного посилання -->
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <!-- Вміст сторінки, який змінюється залежно від маршруту -->
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, onMounted, watch } from "vue";
import EssentialLink from "components/EssentialLink.vue";

const linksList = [
  // Список посилань
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
    // Завантаження збереженої теми з локального сховища
    const savedDarkMode = localStorage.getItem("darkMode");
    const isActiveDarkMode = ref(savedDarkMode === "true");

    // Встановлення початкового значення теми при завантаженні компонента
    onMounted(() => {
      setDarkMode(isActiveDarkMode.value);
    });

    // Спостереження за змінами теми
    watch(isActiveDarkMode, (val) => {
      setDarkMode(val);
    });

    // Функція для зміни теми
    function setDarkMode(val) {
      localStorage.setItem("darkMode", val);
      // Зміна класу тіла сторінки залежно від теми
      document.body.classList.toggle("body--dark", val);
      document.body.classList.toggle("body--light", !val);
    }

    // Функція для оновлення теми
    function darkModeUpdate(value) {
      setDarkMode(value);
    }

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      // Функція для відкриття/закриття висувного меню
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
      isActiveDarkMode,
      darkModeUpdate,
    };
  },
});
</script>
