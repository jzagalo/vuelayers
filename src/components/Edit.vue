<template>
    <div class="sidebar">
        <h2>Base Layers</h2>
        <input type="radio" name="baseLayerRadioButton" value="OSMStandard"  /> OSM Standard
        <input type="radio" name="baseLayerRadioButton" value="OSMHumanitarian"  /> OSMHumanitarian
        <input type="radio" name="baseLayerRadioButton" value="stamenTerrain" checked /> Stamen Terrain

        <button class="zoomIn">Zoom In</button>
        <button class="zoomOut">Zoom Out</button>
        <button class="panToLondon">PanToLondon</button>
        <button class="opacity">Opacity</button>
        <button class="az-1">AZoom to Louisanne</button>
        <form class="form-inline">
          <label>Geometry type &nbsp;</label>
          <select id="type">
            <option value="LineString">LineString</option>
            <option value="Polygon">Polygon</option>
            <option value="Circle">Circle</option>
            <option value="None">None</option>
          </select>
       </form>
    </div>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator';


@Component
export default class Edit extends Vue {


}
</script>

<style scoped>
textarea{
  width: 100%;
  height: 100%;
  resize: none;
}
</style>