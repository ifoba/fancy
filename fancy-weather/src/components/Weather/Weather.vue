<template>
  <div class="weather-list">
    <p @load="update()" class="time">{{cityValue}} {{time}}</p>
      <div class="weather-list_container">
        <WeatherList 
          v-for="weather in weathers" :key="weather.id"
          v-bind:weather="weather"
          v-bind:index="index" 
          v-on:changeDay="changeDay"
          v-bind:celsius="celsius"
        />
      </div>  
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
        time: '',
        date: function() {
          let x = new Date();
          let currentTime = (this.timezone / 60 + x.getTimezoneOffset())*60*1000;
          document.querySelector('form').addEventListener('submit', ()=>{
            clearTimeout(this.update)
          })
          return  new Date(x.getTime()+currentTime).toLocaleString().split(',').join(' ')
        },
        update: setInterval(()=> {this.time = this.date()},1000)
        
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
  margin: 0px;
  padding-left: 10px;
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
  margin: 5px 0;
}
@media  (max-width: 700px){
.time {
  font-size: 30px;
}
}
@media  (max-width: 550px){
.time {
  font-size: 20px;
}
.wrapper {
  flex-direction: column;
}
}

@media  (max-width: 550px){
.time {
  font-size: 18px;
}
}
</style>