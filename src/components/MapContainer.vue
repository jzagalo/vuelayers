<template>
  <div ref="root" style="width:100%; height:100%"> </div>
</template>

<script lang="ts">
import {
    Vue,
    Component,
} from 'vue-property-decorator';
import View from 'ol/View';
import Map from 'ol/Map';
import TileLayer from 'ol/layer/Tile';
import OSM from 'ol/source/OSM';
import VectorLayer from 'ol/layer/Vector';
import VectorSource from 'ol/source/Vector';
import GeoJSON from 'ol/format/GeoJSON';
import 'ol/ol.css';



@Component
export default class MapContainer extends Vue {
    $refs!: {
      root: HTMLDivElement;
    };

    mounted() {
      // this is where we create the OpenLayers map
      new Map({
        // the map will be created using the 'map-root' ref
        target: this.$refs['root'],
        layers: [
          // adding a background tiled layer
          new TileLayer({
            source: new OSM() // tiles are served by OpenStreetMap
          }),
        ],

        // the map view will initially show the whole world
        view: new View({
          zoom: 0,
          center: [0, 0],
          constrainResolution: true
        }),

      });
    }

}
</script>
<style scoped>
  #root{
      position: absolute;
      left:0;
      top:0;
      bottom:0;
      right:0;
  }
</style>

