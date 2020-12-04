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

const data =    {
       "type": "Feature",
       "geometry": {
           "type": "Polygon",
           "coordinates": [
               [
                    [
            -27.0703125,
            43.58039085560784
          ],
          [
            -28.125,
            23.563987128451217
          ],
          [
            -10.8984375,
            32.84267363195431
          ],
          [
            -27.0703125,
            43.58039085560784
          ]
               ]
           ]
       }
       //...
   };


@Component
export default class MapContainer extends Vue {
    $refs!: {
      root: HTMLDivElement;
    };

    mounted() {
        const feature = new GeoJSON().readFeature(data, {
            featureProjection: 'EPSG:3857'
        });

        const vectorLayer = new VectorLayer({
            source: new VectorSource({
                features: [feature]
            }),
        });



      // this is where we create the OpenLayers map
      new Map({
        // the map will be created using the 'map-root' ref
        target: this.$refs['root'],
        layers: [
          // adding a background tiled layer
          new TileLayer({
            source: new OSM() // tiles are served by OpenStreetMap
          }),
          vectorLayer,
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

