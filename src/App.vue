<script setup>
import { ref } from "vue";
import axios from "axios";

const city = ref("");
let result = ref(null);

const capitalizeCity = () => {
  return city.value
    ? city.value
        .split(" ")
        .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
        .join(" ")
    : "";
};

const fetchWeatherData = async () => {
  try {
    const { data } = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&APPID=09acaa3cd57a19340eeae1f3e37a1f3b`
    );
    const { sea_level, grnd_level, ...filteredData } = data.main;
    result.value = filteredData;
  } catch (error) {
    console.error(error);
    result.value = "Ошибка при получении данных.";
  }
};

const touchBtn = () => {
  if (city.value.trim().length < 2) {
    alert("Введите больше символов");
    return;
  }
  fetchWeatherData();
};

const labels = {
  temp: "Температура",
  feels_like: "Ощущается как",
  temp_min: "Минимальная температура",
  temp_max: "Максимальная температура",
  pressure: "Давление",
  humidity: "Влажность",
};
</script>

<template>
  <section class="section-main">
    <div class="wrapper">
      <h1 class="wrapper__caption">Погодное приложение</h1>
      <p class="wrapper__text">
        Узнать погоду в {{ city ? `'${capitalizeCity()}'` : "вашем городе" }}
      </p>
      <div class="container">
        <div class="blackboard">
          <input
            class="wrapper__input"
            type="text"
            v-model="city"
            placeholder="Введите город"
          />
          <button
            class="wrapper__btn"
            :class="city ? 'wrapper__btn--active' : 'wrapper__btn--passive'"
            :disabled="!city"
            @click="touchBtn"
          >
            {{ city ? "Получить данные о погоде" : "Введите название города" }}
          </button>
        </div>
        <ul v-if="result && typeof result === 'object'" class="weather-list">
          <li v-for="(value, key) in result" :key="key" class="weather-item">
            <strong>{{ labels[key] || key }}:</strong> {{ value }}
          </li>
        </ul>
        <p v-else>{{ result }}</p>
      </div>
    </div>
  </section>
</template>

<style lang="scss">
.section-main {
  padding: 0px 15px;
}
.wrapper {
  background-color: lightsalmon;
  border-radius: 100px;
  display: flex;
  text-align: center;
  flex-direction: column;
  gap: 30px;
  min-width: 700px;
  max-width: 900px;
  margin: 0 auto;
  padding: 40px;
  &__caption {
    font-size: 45px;
  }
  &__text {
    font-size: 24px;
    font-weight: 500;
  }
  .blackboard {
    display: flex;
    justify-content: space-between;
    gap: 20px;
  }
  &__input,
  &__btn {
    width: 100%;
    font-size: 18px;
    border-style: none;
    border-radius: 10px;
    padding: 14px 12px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  }

  &__btn {
    color: aliceblue;
    &--active {
      cursor: pointer;
      background-color: rgba(186, 144, 53, 1);
    }
  }
  .container {
    width: fit-content;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    gap: 30px;
  }
}
.weather-list {
  list-style-type: none;
  padding: 0;
  display: grid;
  gap: 10px;
  grid-template-columns: repeat(2, 1fr);
  max-width: fit-content;
  margin: 0 auto;
}

.weather-item {
  background: rgb(34, 193, 195);
  background: linear-gradient(
    183deg,
    rgba(34, 193, 195, 1) 0%,
    rgba(254, 254, 254, 0.9) 24%
  );
  padding: 20px;
  border-radius: 5px;
  font-size: 1.2rem;
  color: black;
  text-align: center;
}

.weather-item strong {
  color: rgba(186, 144, 53, 1);
}

@media screen and (max-width: 768px) {
  .wrapper {
    min-width: unset;
  }
  .weather-item {
    font-size: 14px;
  }
}
@media screen and (max-width: 520px) {
  .weather-item {
    font-size: 12px;
  }
}
@media screen and (max-width: 425px) {
  .wrapper {
    &__input,
    &__btn {
      width: unset;
    }
    .blackboard {
      flex-direction: column;
    }
  }
  .weather-list {
    align-items: center;
    display: flex;
    flex-direction: column;
  }
  .weather-item {
    width: 100%;
    font-size: 10px;
  }
}
</style>
