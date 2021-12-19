<template>
  <div class="app">
    <div
      class="card"
      :class="`${convertTemp(data.main.temp) > 15 ? 'bg-warm' : 'bg-cold'}`"
    >
      <div class="input-wrapper">
        <input
          type="text"
          name="search"
          id=""
          class="search-input"
          placeholder="Search..."
          v-model="city"
          @keydown.enter="get"
        />
        <i @click="get" class="fas fa-search"></i>
      </div>
      <Spinner v-if="isLoading" class="spinner"></Spinner>
      <NotFound v-else-if="notFound" style="z-index: 20"></NotFound>

      <div v-else class="information-box">
        <div class="location-wrapper">
          <div class="location">
            {{ data.name == undefined ? "Tehran" : data.name }} ,
            {{ data.sys.country == "" ? "IR" : data.sys.country }}
          </div>
          <div class="date" v-html="dateFormatChenger()"></div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ convertTemp(data.main.temp) }}°c</div>
          <div class="weather">
            {{ data.weather[0].main === "" ? "Cloud" : data.weather[0].main }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import Spinner from "./components/Spinner.vue";
import NotFound from "./components/NotFound.vue";
export default {
  name: "App",
  data() {
    return {
      api_key: "05e5e1fff65935434abe537e5f0e7608",
      baseUrl: "http://api.openweathermap.org/data/2.5/weather",
      city: "Tehran",
      // currentTime: new Date().toLocaleString(),
      notFound: false,
      data: {
        sys: {
          country: "",
        },
        main: {
          temp: 0,
        },
        weather: [
          {
            main: "",
          },
        ],
      },
      isLoading: false,
    };
  },
  components: {
    Spinner,
    NotFound,
  },
  mounted() {
    this.get();
  },
  methods: {
    convertTemp(kelvinTemp) {
      let degreeTemp = 0;
      if (kelvinTemp == 0) {
        degreeTemp = 0;
      } else {
        degreeTemp = Math.floor(kelvinTemp - 273);
      }
      return degreeTemp;
    },
    get() {
      this.isLoading = true;
      setTimeout(() => {
        axios
          .get(this.baseUrl + "?q=" + this.city + "&appid=" + this.api_key)
          .then((response) => {
            const responseData = response.data;
            this.data = responseData;
            this.notFound = false;
          })
          .catch(() => {
            console.log("tes");
            this.notFound = true;
          })
          .finally(() => {
            this.isLoading = false;
          });
      }, 1500);
    },
    dateFormatChenger() {
      let date = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];

      let day = days[date.getDay()];
      let month = months[date.getMonth()];
      let year = date.getFullYear()
      return `${day} ${date.getDate()} ${month} ${year}`
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@300;500;600;700;800;900&display=swap");
* {
  margin: 0;
  padding: 0;
  // font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
  //   "Lucida Sans", Arial, sans-serif;
}
body {
  font-family: "Montserrat", sans-serif;
}
.app {
  display: flex;
  justify-content: center;
  align-items: center;
  background-image: url("./assets/main1-bg.jpg");
  background-size: cover;
  background-position: bottom;
  .card {
    overflow: hidden;
    margin: 1rem 0;
    width: 100%;
    max-width: 400px;
    height: 95vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    border-radius: 10px;
    background-image: url("./assets/cold-bg.jpg");
    background-size: cover;
    background-position: bottom;
    position: relative;
    transition: all 1s ease;
    @media only screen and (max-width: 450px) {
      height: 100vh;
      margin: 0;
    }
    &.bg-warm {
      background-image: url("./assets/warm-bg.jpg");
    }
    &::after {
      content: "";
      position: absolute;
      top: 0;
      right: 0;
      width: 100%;
      height: 100%;
      background: linear-gradient(
        to bottom,
        rgba(0, 0, 0, 0.212),
        rgba(0, 0, 0, 0.288)
      );
      z-index: 10;
    }
    .input-wrapper {
      z-index: 20;
      position: relative;
      margin-top: 2rem;
      width: 100%;
      display: flex;
      justify-content: center;
      .search-input {
        margin: 0 auto;
        width: 80%;
        transition: all 0.4s ease;
        height: 2.9rem;
        border: 1px solid gray;
        border-top-right-radius: 15px;
        border-bottom-left-radius: 15px;
        padding: 0 1rem;
        font-size: 1.2rem;
        font-family: "Montserrat", sans-serif;
        background: rgba(255, 255, 255, 0.5);
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.445);
        color: rgba(0, 0, 0, 0.678);
        &:focus {
          transition: all 0.4s ease;
          border-bottom-right-radius: 10px;
          border-top-left-radius: 10px;
          border-top-right-radius: 0;
          border-bottom-left-radius: 0;
          outline: none;
          background: rgba(255, 255, 255, 0.856);
          box-shadow: 0 0 20px rgba(0, 0, 0, 0.623);
        }
        &:focus + .fa-search {
          transition: all 0.5s ease;
          animation: shake 0.15s linear 2;
          pointer-events: unset;
          &:hover {
            cursor: pointer;
            transform: scale(1.1) rotate(360deg);
            color: black;
          }
        }
      }
      .fa-search {
        position: absolute;
        right: 12%;
        top: 1rem;
        color: gray;
        font-size: 1.2rem;
        pointer-events: none;
      }
    }
    .spinner {
      margin: 12.5rem 0rem;
      z-index: 20;
    }
    .information-box {
      z-index: 20;
      .location-wrapper {
        width: 100%;
        margin-top: 1.7rem;
        display: flex;
        flex-direction: column;
        align-items: center;
        .location {
          color: white;
          font-size: 2.5rem;
          font-weight: 500;
          text-shadow: 5px 0 5px rgba(0, 0, 0, 0.541);
          @media only screen and(max-width:350px) {
            font-size: 2rem;
          }
        }
        .date {
          color: white;
          font-size: 1.5rem;
          font-style: italic;
          font-weight: 100;
          text-shadow: 5px 0 5px rgba(0, 0, 0, 0.541);
          @media only screen and(max-width:350px) {
            font-size: 1rem;
          }
        }
      }
      .weather-box {
        display: flex;
        flex-direction: column;
        align-items: center;
        z-index: 20;
        margin-top: 1.5rem;
        .temp {
          font-size: 5rem;
          color: white;
          font-weight: 900;
          background: linear-gradient(
            to right,
            rgba(255, 255, 255, 0),
            rgba(255, 255, 255, 0.329)
          );
          padding: 0rem 1rem;
          border-radius: 5px;
          text-shadow: 5px 0 5px rgba(0, 0, 0, 0.404);
          box-shadow: 5px 0 5px rgba(0, 0, 0, 0.404);
        }
        .weather {
          font-size: 2rem;
          font-weight: 700;
          text-shadow: 5px 0 2px rgba(0, 0, 0, 0.253);
          color: white;
          font-style: italic;
          margin-top: 1rem;
        }
      }
    }
  }
}
@keyframes shake {
  0% {
    transform: none;
  }
  25% {
    transform: translateX(-5px);
  }
  50% {
    transform: translateX(0px);
  }
  75% {
    transform: translateX(5px);
  }
  100% {
    transform: translateX(0px);
  }
}
</style>
