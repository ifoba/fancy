<template>
  <div id="app">
    <div class="controlPanel">
      <ControlPanel
      v-bind:content="content"
      v-bind:lang="lang"
      v-bind:celsius="celsius"
      v-on:changeLang="changeLang"
      v-on:changeCity="changeCity"
      v-on:changeDegree="changeDegree"
      v-bind:timezone="timezone" />
    </div>
    <div class="weather">
      <Loader v-if="loading"  />
      <Weather 
        v-else-if="weather.length"
        v-bind:weathers="weather"
        v-bind:content="content" 
        v-bind:lang="lang"
        v-bind:location="location"
        v-bind:celsius="celsius"
        v-bind:cityValue="cityValue"
        v-bind:timezone="timezone" />
        <p v-else>No data</p>
    </div>
  </div>
</template>

<script>
import ControlPanel from './components/ControlPanel'
import Weather from './components/Weather/Weather'
import Loader from './components/Weather/Loader'
import {imgList} from './list'
export default {
  name: 'App',
  data() {
    return {
      weather: [],
      cityValue: '',
      location: '',
      timezone:'',
      celsius: false,
      lang:'',
      loading:  true,
      content: {
        ru: {
          dayWeek: ['Воскресенье', 'Понедельник', 'Вторник', 'Среда', 'Четверг', 'Пятница', 'Суббота'],
          searchBtn: 'Поиск',
          searchInput: 'Введите город или ZIP',
          wind: 'Ветер:',
          humidity: 'Влажность:',
          fl: 'Ощущается как:',
          metric: 'м/с',
          lat: 'Широта',
          lon: 'Долгота'
        },
        en: {
          dayWeek: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
          searchBtn: 'Search',
          searchInput: 'Search city or ZIP',
          wind: 'Wind:',
          humidity: 'Humidity:',
          fl: 'Feels like:',
          metric: 'm/s',
          lat: 'Latitude',
          lon: 'Longitude'
        },
        be: {
          dayWeek: ['Нядзеля', 'Панядзелак', 'Аўторак', 'Серада', 'Чацьвер', 'Пятніца', 'Сыбота'],
          searchBtn: 'Пошук',
          searchInput: 'Пошук горада ці ZIP',
          wind: 'Вецер:',
          humidity: 'Вiлготнасцъ:',
          fl: 'Адчуваецца як:',
          metric: 'м/с',
          lat: 'Шырата',
          lon: 'Даўгата'
        }
      }
    }
  },
  mounted() {
    fetch('https://ipinfo.io/json?token=aa9d4d066e2d0d')
    .then(resGeo => resGeo.json())
    .then(dataGeo => {
      this.location= dataGeo.loc.split(',').reverse();
      fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${dataGeo.city}&lang=en-${this.lang}`)
      .then(resTranslate => resTranslate.json() )
      .then(res => { this.cityValue = res.text[0]})
     fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${dataGeo.city}&lang=${this.lang}&units=metric&appid=3054695a08f59e2c1526d4fd25c27d02`)
     .then(resWeather => resWeather.json())
     .then(dataWeather => {
       this.loading = false
       const noon = 43200;
       const day = 86400;
       const dayWeek = this.content[this.lang].dayWeek;
       this.timezone = dataWeather.city.timezone;
       this.weather = dataWeather.list.filter(item=>item.dt % day === noon).map((el,i)=> {
         return {
           id: i,
           icon: imgList[el.weather[0].icon],
           description: el.weather[0].description,
           temp: Math.round(el.main.temp),
           fahr: Math.round(el.main.temp*1.8 +32),
           fl: Math.round(el.main.feels_like),
           flFahr:Math.round(el.main.feels_like*1.8 +32),
           wind: Math.round(el.wind.speed),
           humidity: Math.round(el.main.humidity),
           date: dayWeek[new Date(el.dt_txt).getDay()]
         }
       });
       if(this.lang === 'be') {
         this.weather.forEach(el => {
           fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${el.description}&lang=en-be`)
          .then(resTranslate => resTranslate.json() )
          .then(res => { el.description = res.text[0]})
         })
       }
     })
   });
  },
  created() {
    if(localStorage.getItem('lang')=== null){
      this.lang = 'en'
    }else{
      this.lang = localStorage.getItem('lang')
    }
    if(localStorage.getItem('deg')=== null){
      this.celsius = true
    }else {
      this.celsius = localStorage.getItem('deg')
    }
  },
  methods: {
    changeLang(str) { 
      this.weather.forEach(el => {
        let index = this.content[this.lang].dayWeek.indexOf(el.date)
        el.date = this.content[str].dayWeek[index]
      });
      fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${this.cityValue}&lang=${this.lang}-${str}`)
      .then(resTranslate => resTranslate.json() )
      .then(res => {this.cityValue = res.text[0]})
      this.weather.forEach(el => {
        fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${el.description}&lang=${this.lang}-${str}`)
        .then(resTranslate => resTranslate.json() )
        .then(res => {el.description = res.text[0]})
      })
      
      this.lang = str;
    },
    changeDegree(){
      this.celsius = !this.celsius
    },
    changeCity(city) {
      let url = '';
      if(/^\d+$/.test(city)){
        url = `https://api.openweathermap.org/data/2.5/forecast?zip=${city},ru&lang=${this.lang}&units=metric&appid=3054695a08f59e2c1526d4fd25c27d02`
      }else {
        url = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&lang=${this.lang}&units=metric&appid=3054695a08f59e2c1526d4fd25c27d02`
      }
      this.loading = true;
      fetch(url)
     .then(resWeather => resWeather.json())
     .then(dataWeather => {
       this.timezone = dataWeather.city.timezone;
       this.loading = false
       const noon = 43200;
       const day = 86400;
       const dayWeek = this.content[this.lang].dayWeek;
       this.weather = dataWeather.list.filter(item=>item.dt % day === noon).map((el,i)=> {
         let description = el.weather[0].description;
         return {
           id: i,
           icon: imgList[el.weather[0].icon],
           description: description,
           temp: Math.round(el.main.temp),
           fahr: Math.round(el.main.temp*1.8 +32),
           fl: Math.round(el.main.feels_like),
           flFahr:Math.round(el.main.feels_like*1.8 +32),
           wind: Math.round(el.wind.speed),
           humidity: Math.round(el.main.humidity),
           date: dayWeek[new Date(el.dt_txt).getDay()]
         }
       })
       fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${city}&lang=en-${this.lang}`)
      .then(resTranslate => resTranslate.json() )
      .then(res => { this.cityValue = res.text[0]})
       if(this.lang === 'be') {
         this.weather.forEach(el => {
           fetch(`https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20200505T091407Z.2a0ab1928c9a0812.1871e129c68597543ce570b0dca45bfdb1eb6906&text=${el.description}&lang=en-be`)
          .then(resTranslate => resTranslate.json() )
          .then(res => { el.description = res.text[0]})
         })
       }
       fetch(`https://api.opencagedata.com/geocode/v1/json?q=${city}&key=c6b6da0f80f24b299e08ee1075f81aa5&pretty=1&no_annotations=1`)
      .then(res => res.json())
      .then(data => {
        this.location = [data.results[0].geometry.lng, data.results[0].geometry.lat]
      })
      .catch(()=>{
        alert('Закончились запросы карты')
      })
     })
     .catch(()=> {
      alert('Ошибка запроса')
      this.loading = false;
     })
    }
  },
  components: {
    ControlPanel,
    Weather,
    Loader,
  }
}
</script>

<style>
body {
  margin: 0;
    background: url('./assets/bridge.jpg');
    background-size: cover ;
    background-position: center;

}
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100vw;
  height: 100vh;


}
.controlPanel {
  width: 90vw;
  padding-top: 10px;
}
.weather {
  width: 90vw;
}
.mapboxgl-map {
  border-radius: 10px;
}

</style>
