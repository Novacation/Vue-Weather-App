<script setup>
import { ref } from "vue";

(function (win) {
  var doc = win.document;

  // If there's a hash, or addEventListener is undefined, stop here
  if (!location.hash && win.addEventListener) {
    //scroll to 1
    window.scrollTo(0, 1);
    var scrollTop = 1,
      getScrollTop = function () {
        return (
          win.pageYOffset ||
          (doc.compatMode === "CSS1Compat" && doc.documentElement.scrollTop) ||
          doc.body.scrollTop ||
          0
        );
      },
      //reset to 0 on bodyready, if needed
      bodycheck = setInterval(function () {
        if (doc.body) {
          clearInterval(bodycheck);
          scrollTop = getScrollTop();
          win.scrollTo(0, scrollTop === 1 ? 0 : 1);
        }
      }, 15);
    win.addEventListener("load", function () {
      setTimeout(function () {
        //reset to hide addr bar at onload
        win.scrollTo(0, scrollTop === 1 ? 0 : 1);
      }, 0);
    });
  }
})(window);

console.log(import.meta.env);

const apiKey = "YOUR_API_KEY",
  urlBase = "https://api.openweathermap.org/data/2.5/",
  query = ref(""),
  weather = ref({});

const fetchWeather = async (ev) => {
  if (ev.key == "Enter") {
    const result = await fetch(
      `${urlBase}weather?q=${query.value}&units=metric&APPID=${apiKey}`
    );
    weather.value = await result.json();
  }
};

const dateBuilder = () => {
  const dateInstance = new Date();
  const months = [
    "January",
    "February",
    "March",
    "April",
    "June",
    "July",
    "August",
    "September",
    "October",
    "November",
    "December",
  ];

  const days = [
    "Sunday",
    "Monday",
    "Tuesday",
    "Wednesday",
    "Thursday",
    "Friday",
    "Saturday",
  ];

  const day = days[dateInstance.getDay()];
  const date = dateInstance.getDate();
  const month = months[dateInstance.getMonth()];
  const year = dateInstance.getFullYear();

  return `${day} ${date} ${month} ${year}`;
};
</script>

<template>
  <div id="background" class="h-screen">
    <main class="h-screen">
      <div class="flex flex-col items-center bg-black h-full bg-opacity-10">
        <input
          class="mt-10 bg-white bg-opacity-90 hover:bg-opacity-100 rounded text-lg w-11/12 p-2 outline-none"
          type="text"
          placeholder="Search"
          @keypress="fetchWeather"
          v-model="query"
        />

        <div
          class="flex flex-col justify-start items-center w-full"
          v-if="typeof weather.main != 'undefined'"
        >
          <p class="text-2xl text-white mt-12">
            {{ weather.name }}, {{ weather.sys.country }}
          </p>

          <p class="text-xl text-white mt-4">{{ dateBuilder() }}</p>

          <div
            id="temp_container"
            class="flex items-center justify-center max-h-40 p-5 text-8xl text-white bg-white bg-opacity-40 rounded-lg w-2/4 mt-8"
          >
            <p class="text-shadow">{{ Math.round(weather.main.temp) }}º</p>
          </div>

          <p class="text-white mt-8 text-4xl">{{ weather.weather[0].main }}</p>
        </div>
      </div>
    </main>
  </div>
</template>

<style>
@import url("./assets/reset.css");

#background {
  background-image: url("@/assets/bg2.jpg");
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: top;
  background-clip: padding-box;
  background-size: cover;
}

#temp_container {
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.text-shadow {
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
