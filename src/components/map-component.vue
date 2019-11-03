<template>
  <div>
    <div ref="mapContainer" class="map-container"></div>
    <soundcloud-modal-component
      v-show="isModalVisible"
      @close="closeModal"
      :url="urlSC"
      :title="title"
    ></soundcloud-modal-component>
    <!-- <iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/698792659&color=%233e3224&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe> -->
  </div>
</template>

<style>
.map-container {
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.ui.selectable.celled.table tbody {
  cursor: pointer;
}

/* .leaflet-draw-toolbar a {
  background-image: url("/images/spritesheet.png");
} */
</style>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import SoundcloudModalComponent from "./soundcloud-modal-component.vue";

/**
 * Problem with Webpack + Leaflet: webpack move icon images and hash there name.
 * Then leaflet path detection is not able to find images anymore. Leaflet members don't seem
 * them want to fix the problem (closing all issues). More Info https://github.com/Leaflet/Leaflet/issues/4968
 * Workaround: delete default icon url and import new local path for each icon.
 */
delete L.Icon.Default.prototype._getIconUrl;
L.Icon.Default.mergeOptions({
  iconRetinaUrl: require("leaflet/dist/images/marker-icon-2x.png"),
  iconUrl: require("leaflet/dist/images/marker-icon.png"),
  shadowUrl: require("leaflet/dist/images/marker-shadow.png")
});

/**
 * @vuese
 * Map component using leaflet library
 * @group map components
 */
export default {
  name: "map-component",
  components: {
    SoundcloudModalComponent
  },
  data() {
    return {
      /**
       * @vuese
       * The leaflet map
       */
      map: null,
      /**
       * @vuese
       * Array containing the geojson object to be displayed as marker on the map
       */
      layer: null,
      isModalVisible: false,
      urlSC: "",
      title: ""
    };
  },
  props: ["geojson"],
  methods: {
    /**
     * @vuese
     * Method to initialise leaflet map container
     */
    initMap() {
      this.map = L.map(this.$refs["mapContainer"], {
        zoomDelta: 0.25,
        zoomSnap: 0,
        center: [44.60840422206536, 4.473356008529663],
        zoom: 15.5,
        maxZoom: 16.75,
        minZoom: 15.5,
        zoomControl: false,
        maxBounds: [
          //south west
          [44.60840422206536, 4.473356008529663],
          //north east
          [44.60840422206536, 4.473356008529663]
        ]
      });
    },

    /**
     * @vuese
     * method to initialise tile layer. This layer is added to the previously defined map container
     */
    initTileLayer() {
      this.tileLayer = L.tileLayer(
        "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
        {
          attribution:
            '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }
      ).addTo(this.map);
    },

    addLocationGeoJson(geojson) {
      if (this.layer != null) {
        this.map.removeLayer(this.layer);
      }

      this.layer = L.geoJSON(geojson, {
        onEachFeature: (feature, layer) => {
          layer.on("click", this.markerClickHandler);
        }
      });
      this.map.addLayer(this.layer);
    },

    markerClickHandler(event) {
      this.showModal();
      this.urlSC = event.target.feature.properties.src;
      this.title = event.target.feature.properties.title;
      //console.log((event.target.feature.properties.src))
      // this.loadPopupContent({
      //   marker: event.target,
      //   config: { maxHeight: this.popupMaxHeight }
      // });
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
      this.urlSC = "";
      this.title = "";
    }
  },
  mounted() {
    /**
     * Initialisation of the map when the component is mounted
     */
    this.initMap();
    this.initTileLayer();
    this.addLocationGeoJson(this.geojson);
  }
};
</script>
