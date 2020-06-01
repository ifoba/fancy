<template>
  <div class="weather-day">
      <span>{{celsius?weatherInfo.temp:weatherInfo.fahr}}°</span>
      <div class="weather-day_info">
          <i v-bind:class="weatherInfo.icon"></i>
          <p class="descrip">{{weatherInfo.description}}</p>
          <p>{{content.fl}} {{celsius ? weatherInfo.fl : weatherInfo.flFahr}}°</p>
          <p>{{content.wind}} {{weatherInfo.wind}}{{content.metric}}</p>
          <p>{{content.humidity}} {{weatherInfo.humidity}}%</p>
          
      </div>
       
  </div>
</template>

<script>
export default {
    props: ['weatherInfo', 'content', 'lang', 'celsius'],
}
</script>

<style scoped>
.icon {
  font-size: 200px;
  color: #ffffff;
  
}
.weather-day {
    display: flex;
}
span {
    display: flex;
    align-items: center;
    margin-left: 60px;
    margin-right:20px ;
    width: 250px;
    color: #ffffff;
    font-family: 'Montserrat', sans-serif;
    font-weight: 600;
    font-size: 250px ;
}
.weather-day_info {

    margin-left: 150px;
}
p{
    color: #ffffff;
    font-family: 'Montserrat', sans-serif;
    font-weight: 600;
    font-size: 25px ;
}
.descrip {
    text-transform: capitalize;
}
</style>