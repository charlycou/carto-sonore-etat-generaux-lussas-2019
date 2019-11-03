<template>
  <div id="app">
    <map-component :geojson="geojson"></map-component>
  </div>
</template>

<script>
import MapComponent from "./components/map-component.vue";
import GEOJSON from "./source.json";

export default {
  name: "App",
  components: {
    MapComponent
  },
  data() {
    return {
      geojson: GEOJSON
    };
  },
  methods: {

  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  /* -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale; */
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
}
</style>
