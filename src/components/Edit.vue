<template>
    <div class="sidebar">
        <h2>Base Layers</h2>
        <input type="radio" name="baseLayerRadioButton" value="OSMStandard"  /> OSM Standard
        <input type="radio" name="baseLayerRadioButton" value="OSMHumanitarian"  /> OSMHumanitarian
        <input type="radio" name="baseLayerRadioButton" value="stamenTerrain" checked /> Stamen Terrain

        <button class="zoomIn">Zoom In</button>    
        <button class="zoomOut">Zoom Out</button>     
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