<template>
  <div ref="root" style="width:100%; height:100%"> </div>
</template>

<script lang="ts">
import {
    Vue,
    Component,
    Prop,
Constructor,
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
    @Prop() geojson!: Record<string, any>;
    private olMap!: Map;
    private vectorLayer!: VectorLayer;

    $refs!: {
      root: HTMLDivElement;
    };

    mounted() {
        const feature = new GeoJSON().readFeature(data, {
            featureProjection: 'EPSG:3857'
        });

        this.vectorLayer = new VectorLayer({
            source: new VectorSource({
                features: []
            }),
        });



      // this is where we create the OpenLayers map
      this.olMap = new Map({
        // the map will be created using the 'map-root' ref
        target: this.$refs['root'],
        layers: [
          // adding a background tiled layer
          new TileLayer({
            source: new OSM() // tiles are served by OpenStreetMap
          }),
          this.vectorLayer,
        ],

        // the map view will initially show the whole world
        view: new View({
          zoom: 0,
          center: [0, 0],
          constrainResolution: true
        }),

      });

      this.olMap.on('pointermove', (event) => {
        const hovered = this.olMap.forEachFeatureAtPixel(
          event.pixel,
          (feature) => feature
        );

        this.$emit('select', hovered);
      });

      this.updateSource(this.geojson);

    }

    updateSource(geojson: any){
      const view = this.olMap.getView();
      const source = this.vectorLayer.getSource();

      const features = new GeoJSON({
        featureProjection: 'EPSG:3857'
      }).readFeature(geojson);

     // source.clear();
      source.addFeature(features);

      // this zooms the view on the created object
      view.fit(source.getExtent());
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

