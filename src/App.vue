<script>
import {API_KEY} from "@/config.js";
import axios from "axios";

export default {
  data() {
    return {
      city: '',
      error: '',
      weather: null
    }
  },
  computed: {
    cityName() {
      return "«" + this.city + "»";
    },
    getTemp() {
      console.log(this.weather);
      return "Температура: " + Math.round(this.weather.main.temp) + "°";
    },
    getFeelsLike() {
      return "Ощущается как: " + Math.round(this.weather.main.feels_like) + "°";
    },
    getHumidity() {
      return "Влажность: " + this.weather.main.humidity + "%";
    },
    getPressure() {
      return "Давление: " + this.weather.main.pressure + " гПа";
    },
    getSunrise() {
      const timestamp = this.weather.sys.sunrise;
      const date = new Date(timestamp * 1000);

      return `Восход солнца: ${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`;
    },
    getSunset() {
      const timestamp = this.weather.sys.sunset;
      const date = new Date(timestamp * 1000);

      return `Закат: ${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`;
    },
    catchError() {
      console.log(this.error);
      if (this.error.cod === "404") return "Город не найден!"
    }
  },
  methods: {
    getWeather() {
      if (this.city.trim().length < 3) return this.error = "Введите более 2-х символов!";

      this.error = "";

      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&lang=ru&units=metric&appid=${API_KEY}`)
          .then(res => this.weather = res.data)
          .catch(err => {
            this.weather = null;
            this.error = err.response.data
          });
    }
  }
}
</script>

<template>
  <div class="wrapper">
    <h1>Погодное приложение</h1>
    <p>Узнайте погоду в {{ city ? cityName : 'любом городе!' }}</p>

    <div class="info-block">
      <div class="info-block-input">
        <input type="text" placeholder="Введите город" v-model="city"/>
        <p v-if="error" class="error">{{ catchError }}</p>
      </div>
      <button type="button" v-if="city" @click="getWeather">Получить погоду</button>
      <button type="button" v-else disabled>Получить погоду</button>
    </div>

    <div v-if="weather" class="weather-block">
      <p>{{ getTemp }}</p>
      <p>{{ getFeelsLike }}</p>
      <p>{{ getHumidity }}</p>
      <p>{{ getPressure }}</p>
      <p>{{ getSunrise }}</p>
      <p>{{ getSunset }}</p>
    </div>
  </div>
</template>

<style scoped>

.wrapper {
  width: 900px;
  height: 500px;
  padding: 20px;
  text-align: center;

  border-radius: 30px;
  background-color: #fff;
}

.wrapper h1 {
  margin-bottom: 10px;
}

.info-block {
  display: flex;
  margin-top: 40px;
  justify-content: center;
}

.info-block input {
  position: relative;
  padding: 12px;
  width: 200px;
  font-size: 15px;
  font-weight: 500;
  outline: none;
  border: none;
  border-bottom: 3px solid #a4a4a4;
  background: transparent;
  transition: all .3s;
}

.error {
  position: absolute;
  margin-top: 5px;
  font-size: 15px;
  color: #991221;
}

.info-block input:focus {
  border-bottom: 3px solid #593283;
}

.info-block button {
  margin-left: 20px;
  background: rgb(106, 34, 240);
  background: linear-gradient(130deg, rgba(106, 34, 240, 1) 0%, rgba(162, 168, 69, 1) 80%);
  font-weight: 600;
  outline: none;
  border: none;
  color: #fff;
  padding: 15px 25px;
  font-size: 14px;
  cursor: pointer;
  border-radius: 10px;
  transition: background 5s;
}

.info-block button:hover {
  background: rgb(162, 168, 69);
  background: linear-gradient(130deg, rgba(162, 168, 69, 1) 20%, rgba(106, 34, 240, 1) 100%);
}

.info-block button:disabled {
  background: rgb(62, 23, 134);
  background: linear-gradient(130deg, rgba(62, 23, 134, 1) 0%, rgba(117, 121, 47, 1) 80%);
  color: #a4a4a4;
}

.info-block button:disabled:hover {
  cursor: not-allowed;
}

.weather-block {
  text-align: center;
  margin-top: 40px;
}

.weather-block p {
  font-size: 16px;
  margin-bottom: 10px;
}

</style>
