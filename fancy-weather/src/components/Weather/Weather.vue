<template>
  <div class="weather-list">
    <p class="time">{{cityValue}} {{new Date(date(timezone)).toLocaleString().slice(0, -3).split(',').join(' ')}}</p>
      <div class="wrapper">
        <WeatherDay
        v-bind:weatherInfo="weathers[index]"
        v-bind:content="content[lang]"
        v-bind:celsius="celsius"
        />
        <mapbox
        v-bind:location="location"
        v-bind:content="content[lang]"
        />
      </div>
      <div class="weather-list_container">
        <WeatherList 
          v-for="weather in weathers" :key="weather.id"
          v-bind:weather="weather"
          v-bind:index="index" 
          v-on:changeDay="changeDay"
          v-bind:celsius="celsius"
        />
      </div>    
  </div>
</template>

<script>
import WeatherList from './WeatherList'
import WeatherDay from './WeatherDay'
import mapbox from '../map/mapbox'

export default {
    props: ['weathers', 'content', 'lang', 'location', 'celsius', 'cityValue', 'timezone'],
    components: {
      WeatherList,
      WeatherDay,
      mapbox
    },
    data() {
      return {
        index: 0,
        date(zone) {
          let x = new Date();
          let currentTime = (zone / 60 + x.getTimezoneOffset())*60*1000;
          console.log(zone)
          return x.getTime()+currentTime
        }
        
        
      }
    },
    methods: {
      changeDay: function(id) {
        this.index = id
      }
    }
}
</script>

<style scoped>
.time{
  margin: 5px;
  background: #353535d5;
  border-radius: 10px;
  font-size: 40px;
  color: #ffffff;
  font-family: 'Montserrat', sans-serif;
}
.wrapper {
  display: flex;
  justify-content: space-between;
  background: #353535d5;
  border-radius: 10px;
}
.weather-list {
  display: flex;
  flex-direction: column;
  margin-top: 5px;
}
.weather-list_container {
  display: flex;
  margin-top: 5px;
}

</style>