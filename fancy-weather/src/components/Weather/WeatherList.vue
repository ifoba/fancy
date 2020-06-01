<template>
  <div class="wether-list_block" 
  v-bind:class="{active: weather.id===index}"
  v-on:click="changeDay"
  >
      <span class="dayWeek">{{weather.date}}</span>
      <div class="info-container">
          <span class="temp">{{celsius ? weather.temp : weather.fahr}}°</span>
          <i v-bind:class="weather.icon"></i>
      </div>
  </div>
</template>

<script>
export default {
    props : ["weather", "index", 'celsius'],
    methods: {
        changeDay() {
            this.$emit('changeDay', this.weather.id)
        }
    }
}
</script>

<style scoped>
.icon {
  font-size: 60px;
  color: #ffffff;
  margin-left: 30px
}
.wether-list_block{
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 17vw ;
    background: #353535d5;
    padding: 10px;
    border-radius: 10px;
    margin-right: 5px;
    font-family: 'Montserrat', sans-serif;
    cursor: pointer;

}
.wether-list_block:hover {
    box-shadow: 0px 10px 10px;
}
.wether-list_block.active {
  background: #353535d5;
  box-shadow: 0px 10px 10px;
}
.dayWeek{
    margin-bottom: 10px;
    font-size: 18px;
    font-weight: 600;
    color: #ffffff;
    font-family: 'Montserrat', sans-serif;
}
.info-container {
    display: flex;
    justify-content: center;
    align-items: center;
}
.temp {
    font-family: 'Montserrat', sans-serif;
    font-size: 30px;
    font-weight: 600;
    color: #ffffff;
}

</style>