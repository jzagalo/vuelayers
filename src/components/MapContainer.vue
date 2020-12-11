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
import Group from 'ol/layer/Group';
import OSM from 'ol/source/OSM';
import VectorLayer from 'ol/layer/Vector';
import VectorSource from 'ol/source/Vector';
import GeoJSON from 'ol/format/GeoJSON';
import 'ol/ol.css';
import Collection from 'ol/Collection';

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
    private zoomInBtn!: HTMLButtonElement;
    private zoomOutBtn!: HTMLButtonElement;

    $refs!: {
      root: HTMLDivElement;
    };

    mounted() {
     

      this.olMap = new Map({
        view: new View({
          center: [-313086.06785608083, 2269873.9919565953],
          zoom: 4,
          maxZoom: 10,
          minZoom: 4
        }),
        target: this.$refs['root'],
      });

      const OSMStandard = new TileLayer({
        source: new OSM(),
        visible: false,
      });

      const OSMHumanitarian = new TileLayer({
        source: new OSM({
          url: 'http://{a-c}.tile.openstreetmap.fr/hot/{z}/{x}/{y}.png'
        }),
        visible: false,
      });

      const stamenTerrain = new TileLayer({
        source: new OSM({
          url: 'http://tile.stamen.com/terrain/{z}/{x}/{y}.jpg',
          attributions: 'Map titles by <a href="http://stamen.com">Stamen</a> '
        }),
        visible: true,
      });

      const layerObject: Record<string, TileLayer> = {
        'OSMStandard': OSMStandard,
        'OSMHumanitarian': OSMHumanitarian,
        'stamenTerrain': stamenTerrain
      };


      const baseLayerGroup = new Group({
        layers: Object.values(layerObject)
      });

      this.olMap.setLayerGroup(baseLayerGroup);
      const baseLayerElements = document.querySelectorAll("div.sidebar>input[type=radio]");

      this.olMap.getLayers().forEach((layer) => {
          baseLayerElements.forEach((inputElement) => {
              const elem = inputElement as HTMLInputElement;
              elem.addEventListener("change", function(){
                 if(elem.checked && layerObject[elem.value] === layer){
                    layer.setVisible(true);
                 } else{
                    layer.setVisible(false);
                 }
              });             
          });
      });

      this.setZoomControls();
    }

    setZoomControls(){
      const zoomInBtn =  document.querySelector(".zoomIn");
      const zoomOutBtn =  document.querySelector(".zoomOut");
      const mapView = this.olMap.getView();

      zoomInBtn?.addEventListener("click", (event) => {
          const zoom = Number(mapView.getZoom());         
          mapView.setZoom(zoom - 1);
      });

      zoomOutBtn?.addEventListener("click", (event) => {
          const zoom = Number(mapView.getZoom());
          mapView.setZoom(zoom + 1);
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

