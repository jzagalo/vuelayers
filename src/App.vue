<template>
    <div id="app">
        <div class="cell cell-map">
            <MapContainer :geojson="geojson" />
        </div>
        <div class="cell cell-edit">
            <Edit  />
        </div>
        <div class="cell cell-inspect">
            <Inspect :feature="selected" />
        </div>  </div>
</template>

<script lang="ts">
import {
    Component,
    Vue,
    Prop,
} from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';
import MapContainer from './components/MapContainer.vue';
import Edit from './components/Edit.vue';
import Inspect from './components/Inspect.vue';

@Component({
    components: {
        HelloWorld,
        MapContainer,
        Edit,
        Inspect
    },
})
export default class App extends Vue {

    selected: any = undefined;
    geojson = {
        type: 'Feature',
        properties: {
            "name": "a fascinating triangle",
            "quality": "top"
        },
        geometry: {
          type: 'Polygon',
          coordinates: [
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
    };

}
</script>

<style>
html, body {
    height: 100%;
    margin: 0;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    height: 100%;
    display: grid;
    grid-template-columns: 100vh;
    grid-auto-rows: 1fr;
    grid-gap: 1rem;
    padding: 1rem;
    box-sizing: border-box;
}

.cell {
    border-radius: 4px;
    background-color: lightgrey;
}

.cell-map {
    grid-column: 1;
    grid-row-start: 1;
    grid-row-end: 3;
}

.cell-edit {
    grid-column: 2;
    grid-row: 1;
}

.cell-inspect {
    grid-column: 2;
    grid-row: 2;
}

</style>
