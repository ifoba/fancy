<template>
  <div class="container">
    <MglMap
    :accessToken="accessToken" 
    :mapStyle="mapStyle"
    @load="onMapLoad" />
    <span>{{content.lat}}: {{location[0]}}</span>
    <span>{{content.lon}}: {{location[1]}}</span>

  </div>
</template>

<script>
import Mapbox from "mapbox-gl";
import { MglMap } from "vue-mapbox";
export default {
  props: ['location', 'content'],
  data() {
      return {
        accessToken: "pk.eyJ1IjoiZm9iYSIsImEiOiJja2FqODF3cHIwODFzMnFwZXh6eTZlbHBoIn0.5rqSxUclup5I15EMEbL2SQ", 
        mapStyle: 'mapbox://styles/mapbox/streets-v11',
      }
  },
  components: {
      MglMap
  },
  methods: {
    async onMapLoad(event) {
      const asyncActions = event.component.actions

      const newParams = await asyncActions.flyTo({
        center: this.location,
        zoom: 9,
        speed: 100
      })
      
    }},
  created() {
    this.mapbox = Mapbox;
  }
}
</script>

<style scoped>
.container{
  display: flex;
  flex-direction: column;
  width: 450px;
  height: 450px;
  margin-top: 20px;
  margin-right: 10px;
}
span {
  font-family: 'Montserrat', sans-serif;
  color: #ffffff;
  font-size: 20px;
}
@media  (max-width: 1300px){
.container {
  width: 35vw;
  height: 35vw;
}
}
@media  (max-width: 550px){
.container {
  width: 90vw;
  height: 45vw;
}
}

</style>