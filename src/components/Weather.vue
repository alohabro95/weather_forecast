<script setup>
import { ref } from "vue";
import axios from "axios";

const city = ref("");
let result = ref(null);
const touchBtn = () => {
  if (city.value.trim().length < 2) {
    alert("Введите больше символов");
    return false;
  }
  axios
    .get(
      `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&APPID=09acaa3cd57a19340eeae1f3e37a1f3b`
    )
    .then((res) => {
      result.value = res.data.main;
    })
    .catch((error) => {
      console.error(error);
      result.value = "Ошибка при получении данных.";
    });
};

const showCity = () => {
  return `'${city.value}'`;
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
  <div class="wrapper">
    <h1 class="wrapper__caption">Погодное приложение</h1>
    <p class="wrapper__text">
      Узнать погоду в {{ city === "" ? "вашем городе" : showCity() }}
    </p>
    <div class="blackboard">
      <input
        class="wrapper__input"
        type="text"
        v-model="city"
        placeholder="Введите город"
      />
      <button
        class="wrapper__btn wrapper__btn--active"
        v-if="city !== ''"
        @click="touchBtn"
      >
        Получить данные о погоде
      </button>
      <button class="wrapper__btn wrapper__btn--passive" v-else disabled>
        Введите название города
      </button>
    </div>

    <ul v-if="result && typeof result === 'object'" class="weather-list">
      <li v-for="(value, key) in result" :key="key" class="weather-item">
        <strong>{{ labels[key] || key }}:</strong> {{ value }}
      </li>
    </ul>
    <p v-else>{{ result }}</p>
  </div>
</template>

<style lang="scss">
.wrapper {
  display: flex;
  text-align: center;
  flex-direction: column;
  gap: 30px;
  width: 900px;
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
    gap: 20px;
    margin: 0 auto;
  }
  &__input,
  &__btn {
    font-size: 18px;
    border-style: none;
    border-radius: 10px;
    padding: 12px 10px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  }

  &__btn {
    color: aliceblue;
    max-width: 180px;
    &--active {
      cursor: pointer;
      background-color: rgba(186, 144, 53, 1);
    }
  }
}
.weather-list {
  list-style-type: none;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(1fr, 3);
}

.weather-item {
  background-color: #f0f0f0;
  padding: 10px;
  border-radius: 5px;
  font-size: 1.2rem;
  color: #333;
}

.weather-item strong {
  color: #6d1373;
}
</style>
