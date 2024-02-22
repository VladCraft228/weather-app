<template>
  <div>
    <h3>What's the weather like in the city?</h3>

    <!-- Поле пошуку за назвою міста, які відображається, коли не використовуються координати -->
    <div v-if="!useCoordinates">
      <q-input
        outlined
        v-model="city"
        placeholder="Enter the name of the city"
        :dense="dense"
        @keyup.enter="getWeather"
        label-position="right"
      >
        <!-- Кнопка для пошуку погоди за назвою міста -->
        <template v-slot:append>
          <q-icon style="cursor: pointer" @click="getWeather" name="search">
          </q-icon>
        </template>
      </q-input>
    </div>

    <!-- Поля для введення координат, які відображаються, коли використовуються координати -->
    <div class="coord-input" v-else>
      <q-input
        type="number"
        outlined
        v-model="latitudeLeft"
        label="Latitude (integer part)"
        :dense="dense"
      />
      <q-input
        type="number"
        outlined
        v-model="latitudeRight"
        label="Latitude (decimal part)"
        :dense="dense"
      />
      <q-input
        type="number"
        outlined
        v-model="longitudeLeft"
        label="Longitude (integer part)"
        :dense="dense"
      />
      <q-input
        type="number"
        outlined
        v-model="longitudeRight"
        label="Longitude (decimal part)"
        :dense="dense"
      />

      <!-- Кнопка для пошуку погоди за координатами міста -->
      <q-btn label="Search" color="primary" @click="getWeatherByCoordinates" />
    </div>

    <!-- Перемикач для вибору режиму пошуку за координатами або назвою міста -->
    <q-toggle v-model="useCoordinates" label="Search by coordinates" />
    <div v-if="weather">
      <!-- Код для відображення погодних даних, коли вони доступні -->
      <h2>
        The weather in {{ weather.name }}
        <q-icon :name="getWeatherIcon(weather.weather[0].description)" />
      </h2>
      <p>Temperature: {{ weather.main.temp }}°C</p>
      <p>Feeling like: {{ weather.main.feels_like }}°C</p>
      <p>Description: {{ weather.weather[0].description }}</p>
      <p>Wind speed: {{ weather.wind.speed }}</p>
    </div>
  </div>
</template>

<script>
export default {
  // Дані, які використовуються в компоненті
  data() {
    return {
      city: "",
      latitudeLeft: 0,
      latitudeRight: 0,
      longitudeLeft: 0,
      longitudeRight: 0,
      weather: null,
      dense: true,
      useCoordinates: false,
    };
  },
  methods: {
    // Метод для отримання погодних даних за містом
    getWeather() {
      const API_KEY = "ea8f3c3618b948e676e5d468c0ca7bfd";
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${API_KEY}&units=metric`;

      // Перевірка на пусту строку
      if (!this.city.trim()) {
        this.$q.notify({
          type: "negative",
          message: "Please enter a city name",
        });
        return; // Повертаємо, щоб не виконувати запит на сервер
      }
      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          if (data.cod === "404") {
            this.$q.notify({
              type: "negative",
              message: "This city does not exist, try something else",
            });
          } else {
            this.weather = data;
          }
        });
    },

    // Метод для отримання погоди за координатами
    getWeatherByCoordinates() {
      // Перевірка на пусті поля для координат
      if (
        this.latitudeLeft === null ||
        this.latitudeLeft === undefined ||
        this.latitudeLeft === "" ||
        this.latitudeRight === null ||
        this.latitudeRight === undefined ||
        this.latitudeRight === "" ||
        this.longitudeLeft === null ||
        this.longitudeLeft === undefined ||
        this.longitudeLeft === "" ||
        this.longitudeRight === null ||
        this.longitudeRight === undefined ||
        this.longitudeRight === ""
      ) {
        // Перевірка на пусті поля для координат
        this.$q.notify({
          type: "negative",
          message: "Please enter all latitude and longitude values",
        });
        return;
      }

      const latitude = Number(`${this.latitudeLeft}.${this.latitudeRight}`);
      const longitude = Number(`${this.longitudeLeft}.${this.longitudeRight}`);
      const API_KEY = "ea8f3c3618b948e676e5d468c0ca7bfd";
      const url = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${API_KEY}&units=metric`;

      // Код перехоплення міст, яких не існує в API OpenWeatherMap
      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          if (data.cod === "400") {
            this.$q.notify({
              type: "negative",
              message: "This city does not exist, try something else",
            });
          } else {
            this.weather = data;
          }
        });
    },

    // Метод для відображення відповідного значка погоди на основі опису погоди
    getWeatherIcon(weatherDescription) {
      switch (weatherDescription) {
        // Група чистої погоди
        case "clear sky":
          return "bi-cloud-sun-fill";

        // Група хмарної погоди
        case "few clouds":
        case "scattered clouds":
        case "broken clouds":
        case "overcast clouds":
          return "bi-cloud-fill";

        // Група дощової погоди
        case "shower rain":
        case "rain":
        case "light rain":
        case "moderate rain":
        case "freezing rain":
        case "light intensity shower rain":
          return "bi-cloud-rain-fill";
        case "heavy intensity rain":
        case "very heavy rain":
        case "extreme rain":
        case "shower rain":
        case "heavy intensity shower rain":
        case "ragged shower rain":
          return "bi-cloud-rain-heavy-fill";

        // Група мрячної погоди
        case "light intensity drizzle":
        case "drizzle":
        case "heavy intensity drizzle":
        case "light intensity drizzle rain":
        case "drizzle rain":
        case "heavy intensity drizzle rain":
        case "shower rain and drizzle":
        case "heavy shower rain and drizzle":
          return "bi-cloud-drizzle-fill";

        // Група штормової погоди
        case "thunderstorm":
        case "light thunderstorm":
        case "heavy thunderstorm":
        case "ragged thunderstorm":
          return "bi-cloud-lightning-fill";

        case "thunderstorm with light rain":
        case "thunderstorm with rain":
        case "thunderstorm with heavy rain":
        case "thunderstorm with light drizzle":
        case "thunderstorm with drizzle":
        case "thunderstorm with heavy drizzle":
          return "bi-cloud-lightning-rain-fill";

        // Група сніжної погоди
        case "snow":
        case "light snow":
        case "heavy snow":
        case "light shower snow":
        case "shower snow":
        case "heavy shower snow":
          return "bi-cloud-snow-fill";

        case "sleet":
        case "light shower sleet":
        case "shower sleet":
        case "light rain and snow":
        case "rain and snow":
          return "bi-cloud-sleet-fill";

        // Група різних погодних явищ
        case "mist":
        case "smoke":
        case "fog":
        case "sand/dust whirls":
        case "sand":
        case "dust":
        case "volcanic ash":
          return "bi-cloud-fog-fill";
        case "haze":
          return "bi-cloud-haze-fill";
        case "squalls":
          return "bi-cloud-rain-heavy-fill";
        case "tornado":
          return "bi-tornado";

        // Погода за замовчанням
        default:
          return "bi-cloud-sun-fill";
      }
    },
  },
};
</script>
