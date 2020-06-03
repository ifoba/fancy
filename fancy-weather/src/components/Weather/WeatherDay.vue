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
    data() {
        return {
            description() {
                return this.weatherInfo.description + 123
            }
        }
    }
}
</script>

<style scoped>
.icon {
  margin-top: 15px;
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
@media  (max-width: 1300px){
.icon{
    font-size: 150px;
    }
span {
    width: 15vw;
    margin-left: 3vw;
    font-size: 150px ;
}
.weather-day_info {
    margin-left: 8vw;
}
p {
    font-size: 20px;
}
}
@media  (max-width: 700px){
.icon{
    font-size: 100px;
    }
span{
    font-size: 100px;
    }
p {
    font-size: 15px;
}
}


@media  (max-width: 550px){
.weather-day {
    width: 90vw;
}
span {
    width:35vw ;
}
.weather-day_info {
    width: 30vw;
}
}
</style>