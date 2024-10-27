<script>
import axios from "axios";
import { ref, onMounted } from "vue";

export default {
  setup() {
    const city = ref("tehran");
    const cityInput = ref("");
    const weatherImage = ref("");
    const windLabel = ref("");
    const weatherLabel = ref("");
    const weatherDegree = ref("");
    const theme = ref("");
    const loading = ref(false);

    const codeMap = {
      Clear: { title: "Clear", theme: "clear" },
      Clouds: { title: "Partly Cloudy", theme: "partly-cloudy" },
      Rain: { title: "Rainy", theme: "rainy" },
      Snow: { title: "Snowy", theme: "snowy" },
      sunny: { title: "Sunny", theme: "sunny" },
      // Add other mappings as needed
    };

    const toggleLoading = (isLoading) => {
      loading.value = isLoading;
    };

    const getWeather = async (cityName) => {
      try {
        toggleLoading(true);
        const response = await axios.get(
          "https://api.openweathermap.org/data/2.5/weather",
          {
            params: {
              q: cityName,
              appid: "9a2c7996521ab8da77c5ed5da436da87",
              units: "metric",
            },
          }
        );
        updateView(response.data);
      } catch (error) {
        alert(
          "Failed to fetch weather data. Please check the city name and try again."
        );
      } finally {
        toggleLoading(false);
      }
    };

    const updateView = (data) => {
      const weather = data.weather[0];
      const config = codeMap[weather.main] || {
        title: "",
        theme: "",
      };
      city.value = data.name;
      weatherImage.value = `https://openweathermap.org/img/wn/${weather.icon}@2x.png`;
      windLabel.value = `${data.wind.speed} km/h`;
      weatherDegree.value = `${Math.round(data.main.temp)}º`;
      weatherLabel.value = config.title;
      theme.value = config.theme;
    };

    const getCity = () => {
      const cityName = cityInput.value.trim();
      if (cityName) {
        getWeather(cityName);
      } else {
        alert("Please enter a valid city");
      }
    };

    onMounted(() => {
      getWeather(city.value);
    });

    return {
      city,
      cityInput,
      weatherImage,
      windLabel,
      weatherLabel,
      weatherDegree,
      theme,
      loading,
      getCity,
    };
  },
};
</script>

<template>
  <div
    class="flex justify-center items-center flex-col scaffold"
    :data-theme="theme"
  >
    <div class="flex items-center justify-between header">
      <div class="flex items-center">
        <div class="icon">
          <img src="./assets/icons/map.png" alt="" />
        </div>
        <p class="header__city">{{ city }}</p>
      </div>
    </div>
    <div class="flex flex-col weather">
      <div class="flex flex-col justify-center items-center weather-box">
        <div class="weather__img">
          <img :src="weatherImage" alt="" class="weather__img-elem" />
        </div>
        <div
          class="flex flex-col justify-between items-center weather__details"
        >
          <p class="weather__label">{{ weatherLabel }}</p>
          <p class="weather__degree">{{ weatherDegree }}</p>
          <div class="flex items-center weather__wind">
            <div class="weather__wind-icon">
              <img src="./assets/icons/wind.png" alt="" />
            </div>
            <p class="weather__wind-label">{{ windLabel }}</p>
          </div>
        </div>
        <div class="search">
          <input
            class="search__input"
            type="text"
            placeholder="City name"
            v-model="cityInput"
          />
        </div>
        <div class="actions">
          <button class="actions__find" @click="getCity">Find City</button>
        </div>
      </div>
      <div class="flex flex-col">
        <div class="loading-indicator" v-if="loading"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
@font-face {
  font-family: Roboto;
  src: url("./assets/fonts/Roboto-Bold.ttf") format("truetype");
  font-weight: 700;
}

@font-face {
  font-family: Roboto;
  src: url("./fonts/Roboto-Medium.ttf") format("truetype");
  font-weight: 500;
}

@font-face {
  font-family: Roboto;
  src: url("./fonts/Roboto-Regular.ttf") format("truetype");
  font-weight: 400;
}

* {
  box-sizing: border-box;
  transition: color 0.15s ease-out, background 0.15s ease-out;
}

body {
  margin: 0;
  font-family: Roboto;
}

p,
h1,
h2,
h3,
h4,
h5,
h6,
ul {
  margin: 0;
}

.scaffold {
  min-height: 100vh;
  overflow: hidden;
}

.icon {
  width: 1.5rem;
  height: 1.5rem;
}

.scaffold .header,
.scaffold .weather {
  min-width: 300px;
  padding: 1rem;
  border-radius: 0.75rem;
  background-color: #eff7f054;
  box-shadow: 0 0.25rem 1.5rem -0.125rem rgba(0, 0, 0, 0.08);
}

.scaffold .header {
  margin-bottom: 0.5rem;
}

/* .header__time, */
.header__city {
  font-weight: 700;
  font-size: 0.875rem;
  color: var(--shade-middle);
  margin-left: 0.25rem;
}

.header__time {
  font-weight: 500;
  font-size: 0.75rem;
  color: var(--time-color);
}

.weather .weather__img {
  margin: 1.5rem 0 2.5rem;
  width: 7rem;
}

.icon img,
.weather__wind-icon img,
.weather .weather__img img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.weather__label {
  font-weight: 500;
  font-size: 1rem;
  color: var(--label-color);
}

.weather__degree {
  font-weight: 700;
  font-size: 4rem;
  color: rgba(0, 0, 0, 0.87);
  margin-top: 0.375rem;
  margin-left: 1rem;
}

.weather__wind {
  opacity: 0.48;
  margin: 1rem 0 3rem;
}

.weather__wind-icon {
  width: 1.5rem;
  margin-right: 0.5rem;
}

.weather__wind-label {
  font-size: 0.875rem;
  font-weight: 500;
  color: rgba(0, 0, 0, 0.87);
}

.search {
  border-radius: 0.5rem;
  background-color: var(--shade-light);
  width: calc(100% - 2rem);
}

.search__input {
  color: var(--dark-shade);
  font-family: inherit;
  width: 100%;
  outline: none;
  border: none;
  background-color: transparent;
  font-size: 1rem;
  padding: 0.675rem 0.75rem;
}

.actions {
  width: calc(100% - 2rem);
}

.actions button {
  background-color: var(--shade-middle);
  border-radius: 0.5rem;
  border: none;
  outline: none;
  font-size: 1rem;
  width: 100%;
  padding: 0.75rem 0.75rem;
  color: rgba(255, 255, 255, 0.87);
  margin: 0.5rem 0;
  font-weight: 500;
}

.loading-indicator {
  width: 2rem;
  height: 2rem;
  border: 0.375rem solid var(--shade-light);
  border-radius: 50%;
  border-top: 0.375rem solid var(--dark-shade);
  animation: spin 1s ease-out infinite;
  align-self: center;
  margin: 1.5rem;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.scaffold[data-theme="sunny"] {
  background-image: linear-gradient(to top, #f9d423 0%, #ff4e50 100%);

  --dark-shade: #f39c12;
  --shade-middle: #f1c40f;
  --shade-light: #f7dc6f;
  --time-color: #f7b731;
  --label-color: #d35400;
}

.scaffold[data-theme="partly-cloudy"] {
  background-image: linear-gradient(to top, #797d7f 0%, #e2ebf0 100%);

  --dark-shade: #6d7993;
  --shade-middle: #243f51;
  --shade-light: #dce3ee;
  --time-color: #aec6cf;
  --label-color: #5f6f81;
}

.scaffold[data-theme="snowy"] {
  background-image: linear-gradient(to top, #e6f1f5 0%, #ffffff 100%);

  --dark-shade: #95a5a6;
  --shade-middle: #bdc3c7;
  --shade-light: #ecf0f1;
  --time-color: #dfe6e9;
  --label-color: #7f8c8d;
}

.scaffold[data-theme="rainy"] {
  background-image: linear-gradient(to top, #83a4d4 0%, #b6fbff 100%);

  --dark-shade: #2980b9;
  --shade-middle: #3498db;
  --shade-light: #d1e8ff;
  --time-color: #5dade2;
  --label-color: #1f618d;
}

.scaffold[data-theme="cloudy"] {
  background-image: linear-gradient(to top, #bdc3c7 0%, #2c3e50 100%);

  --dark-shade: #34495e;
  --shade-middle: #7f8c8d;
  --shade-light: #ecf0f1;
  --time-color: #95a5a6;
  --label-color: #2c3e50;
}

.scaffold[data-theme="clear"] {
  background-image: linear-gradient(to top, #a1c4fd 0%, #c2e9fb 100%);

  --dark-shade: #2980b9;
  --shade-middle: #224c68;
  --shade-light: #ecf0f1;
  --time-color: #d6eaf8;
  --label-color: #2874a6;
}
</style>
