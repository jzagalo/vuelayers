<template>
  <div ref="root" class="map" style="width:100%; height:90%">  </div>
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
import { Vector as VectorSource} from 'ol/source';
import GeoJSON from 'ol/format/GeoJSON';
import Draw from 'ol/interaction/Draw'
import 'ol/ol.css';
import Collection from 'ol/Collection';
import { transform } from "ol/proj";
import { Vector } from "ol/source";
import Polygon from 'ol/geom/Polygon';
import Geometry from 'ol/geom/Geometry';
import SimpleGeometry from 'ol/geom/Geometry';
import Feature from 'ol/Feature';
import { Extent } from 'ol/extent';
import { DragRotateAndZoom, defaults as defaultInteractions } from 'ol/interaction';
import { OverviewMap, defaults as defaultControls } from 'ol/control';
import GeometryType from 'ol/geom/GeometryType';



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
    private londonProjection = transform([-0.12755, 51.507222], 'EPSG:4326', 'EPSG:3857');
    private olView!: View;
    private overViewMapControl!: OverviewMap;

    private activeLayer: any;


    $refs!: {
      root: HTMLDivElement;
    };

    mounted() {
      this.olView =  new View({
          center: [-11000000, 4600000],
          zoom: 4,
          maxZoom: 10,
          minZoom: 4
      });


      const tileLayer =  new TileLayer({
          source: new OSM({ 'url': 'https://tile.thunderforest.com/cycle/{z}/{x}/{y}.png?apikey=01524d483cd44b66ab2021708cf53527' })
      });
      const vectorLayer =   new VectorLayer({
          source:  new VectorSource({  wrapX: false }),
      });

      this.overViewMapControl = new OverviewMap({
        className: 'ol-overviewmap ol-custom-overviewmap',
        layers: [
          tileLayer, vectorLayer
        ],
        collapseLabel: '\u00BB',
        label: '\u00AB',
        collapsed: false,
      });

      const overViewCopy = this.overViewMapControl;
      this.olMap = new Map({
        view:this.olView,
        target: this.$refs['root'],
        controls: defaultControls().extend([overViewCopy]),
        interactions: defaultInteractions().extend([new DragRotateAndZoom()])
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
      let layerCopy = this.activeLayer;
      this.olMap.getLayers().forEach((layer) => {

          baseLayerElements.forEach((inputElement) => {
              const elem = inputElement as HTMLInputElement;
              elem.addEventListener("change", () => {
                 if(elem.checked && layerObject[elem.value] === layer){
                    layer.setVisible(true);
                    layerCopy = layer;
                 } else{
                    layer.setVisible(false);
                 }
              });
          });
      });

      this.setZoomControls();
      this.setPanToLondon();
      this.setAdvanced();
      this.mapControls();
      this.freeHandDraw();
    }

    setZoomControls(){
      const zoomInBtn =  document.querySelector(".zoomIn");
      const zoomOutBtn =  document.querySelector(".zoomOut");
      const mapView = this.olMap.getView();

      zoomInBtn?.addEventListener("click", () => {
          const zoom = Number(mapView.getZoom());
          mapView.setZoom(zoom - 1);
      });

      zoomOutBtn?.addEventListener("click", () => {
          const zoom = Number(mapView.getZoom());
          mapView.setZoom(zoom + 1);
      });
    }

    setPanToLondon(){
      const panToLondon =  document.querySelector(".panToLondon");
      panToLondon?.addEventListener("click", () => {
        this.olView.animate({
          center: this.olView.getCenter(),
          duration: 1000,
          zoom: 7
        });
      });
    }

    setAdvanced(){
      const source = new VectorSource({
        url: '../data/geojson.json',
        format: new GeoJSON()
      });

      const avLousine = document.querySelector('.az-1');
      avLousine?.addEventListener("click", () => {
         const feature: any = source.getFeatures()[1];
         const point: any = feature.getGeometry()!;
         this.olView.fit(point, { padding: [170, 50, 30, 150] });
      });
    }

    mapControls(){
      const rotateWithView = document.querySelector('.rotateWithView')!;
      const overViewCopy = this.overViewMapControl;
      (rotateWithView as HTMLInputElement).addEventListener('change', function(){
        overViewCopy.setRotateWithView(this.checked);
      });
    }

    freeHandDraw(){
      let draw!: Draw;
      const typeSelect = document.getElementById("type")!;
      const source = new VectorSource({wrapX: false});
      const addInteraction = () =>{
        const value = (typeSelect as HTMLSelectElement).value;
        if(value !== 'None'){
          draw = new Draw({
            source: source,
            type: ((typeSelect as HTMLSelectElement).value as GeometryType),
            freehand: true
          });

          this.olMap.addInteraction(draw);
        }
      };

      typeSelect.addEventListener("change", ()=>{
        this.olMap.removeInteraction(draw);
        addInteraction();
      });
    }

}
</script>
<style scoped>
  #root{
      position: absolute;
      left:0;
      top:0;
      bottom:30;
      right:0;
  }

  #root .ol-custom-overviewmap,
  #root .ol-custom-overviewmap.ol-uncollapsible {
    right: 0;
    top: 0;
  }

  #root .ol-custom-overviewmap:not(.ol-collapsed)  {
    border: 1px solid black;
  }

  #root .ol-custom-overviewmap .ol-overviewmap-map {
    border: none;
    width: 300px;
  }

  #root .ol-custom-overviewmap .ol-overviewmap-box {
    border: 2px solid red;
  }

  #root .ol-custom-overviewmap:not(.ol-collapsed) button{
    bottom: auto;
    left: auto;
    right: 1px;
    top: 1px;
  }

  #root .ol-rotate {
    top: 170px;
    right: 0;
  }
</style>

