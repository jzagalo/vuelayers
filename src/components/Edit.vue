<template>
    <textarea v-model="geojsonEdit"></textarea>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';


@Component
export default class Edit extends Vue {
    @Prop() geojson!: Record<string, any>;

    set geojsonEdit(value: any){
        this.$emit('change', JSON.parse(value));
    }
    get geojsonEdit(){
        return JSON.stringify(this.geojson, null, '');
    }

}
</script>

<style scoped>
textarea{
  width: 100%;
  height: 100%;
  resize: none;
}
</style>