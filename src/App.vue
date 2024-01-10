<template>
  <div class="flex flex-col min-h-screen bg-gradient-to-t from-sky-500 to-indigo-500 items-center text-white">
    <div class="w-full h-16 flex justify-center items-center shadow-md shadow-cyan-900 space-x-4">
      <img src="./assets/logo.png" class="h-12 w-12 rounded-full">
      <h1>Météo locale</h1>
    </div>
    <div class="p-4">
      <input class="border-b-slate-500 border-b-2 focus:outline-none shadow-md focus:shadow-cyan-900 rounded-md space-x-4 space-y-4 text-slate-500" type="text" v-model="search" @keypress.enter="getResult()" placeholder="Rechercher par ville"/>
      <button class="rounded-md m-2 p-1 bg-indigo-800 hover:bg-slate-50 hover:text-sky-700 shadow-md shadow-cyan-900" @click="getResult()">Valider</button> 
    </div>
    <div v-if=" mapboxResult.length > 0" class="p-6 flex flex-col justify-center items-center bg-indigo-500 rounded-lg shadow-xl shadow-cyan-600/20">
      <h2 class="text-2xl">{{ mapboxResult[0].place_name }}</h2>
      <h3 class="text-3xl mt-2"> {{ Math.round(openWeatherResult.main.temp) }}°C</h3>
      <img class="w-40 h-40" :src="`http://openweathermap.org/img/wn/${openWeatherResult.weather[0].icon}@2x.png`"  alt="icon_weather"/>
      <p>{{ openWeatherResult.weather[0].description }}</p>
      <p class="mt-2">Température ressentie:{{ Math.round(openWeatherResult.main.feels_like) }}°C</p>
       <div class="flex flex-row mt-2">
        <p>min: {{ Math.round(openWeatherResult.main.temp_max) }}°C</p>
        <p class="ml-3">max: {{ Math.round(openWeatherResult.main.temp_max) }}°C</p>
       </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

const search = ref('');
const mapboxResult = ref([]);
const mapboxKey = 'pk.eyJ1IjoiY2hhcmxvdHRlLXJudCIsImEiOiJjbGVnMWNpNmMwMmV5NDZxbHBxdjlzbWc5In0.eLRzFAEJKEd7FuUuqEpyNQ';
const openWeatherKey = '00342e5ade226d805a485a273c042e34';
const openWeatherResult = ref([]);

const getResult = async () => {
if( search.value != ''){
 await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${mapboxKey}&types=place`)
 .then(res => mapboxResult.value = res.data.features);

  await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${mapboxResult.value[0].geometry.coordinates[1]}&lon=${mapboxResult.value[0].geometry.coordinates[0]}&appid=${openWeatherKey}&units=metric&lang=fr`)
 .then(res => openWeatherResult.value = res.data);
 search.value = '';
 }
}
</script>

