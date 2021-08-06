<template>
  <div class="row map">
    <l-map
        :zoom="zoom"
        :center="center"
        style="height: 500px; width: 100%"
    >
      <l-geo-json
          v-if="show"
          :geojson="geojson"
          :options="options"
          :options-style="styleFunction"
      />
      <!--<l-marker :lat-lng="marker" /> -->
    </l-map>

  </div>

</template>

<script>
import L from 'leaflet';
import { LMap, LGeoJson } from 'vue2-leaflet';

export default {
  name: "AbilaMap",
  data() {
    return {
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 13,
      center: [36.0700, 24.8670],
      geojson: null,
      fillColor: "#e4ce7f",
      marker: L.latLng(36.0700, 24.8670)
    };
  },
  components: {
    LMap,
    LGeoJson,
  },
  computed: {
    options() {
      return {
        onEachFeature: this.onEachFeatureFunction
      };
    },
    styleFunction() {
      const fillColor = this.fillColor; // important! need touch fillColor in computed for re-calculate when change fillColor
      return () => {
        return {
          weight: 2,
          color: "#ECEFF1",
          opacity: 1,
          fillColor: fillColor,
          fillOpacity: 1
        };
      };
    },
    onEachFeatureFunction() {
      if (!this.enableTooltip) {
        return () => {};
      }
      return (feature, layer) => {
        layer.bindTooltip(
            "<div>" + feature.properties.FETYPE + ' ' +
            feature.properties.FENAME +
            "</div>",
            { permanent: false, sticky: true }
        );
      };
    }
  },
  async created() {
    this.loading = true;
    const response = await fetch("/data/Abila.json")
    const data = await response.json();
    this.geojson = data;
    this.loading = false;
  }
}
</script>

<style scoped>
.map{
  height: 60vh;
  width: 100%;
}
</style>