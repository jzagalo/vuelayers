<template>
    <ul>
        <li :key="prop" v-for="prop in props()">
            <b>{{prop}}: </b> {{ feature.get(prop) }}
        </li>
    </ul>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';
import Feature from 'ol/Feature';


@Component
export default class Inspect extends Vue {
    @Prop() feature!: Feature;

    props(){
        return this.feature ? this.feature.getKeys().
            filter(key => key !== this.feature.getGeometryName()) : [];
    }

}
</script>

<style scoped>
ul{
  list-style: none;
}
</style>