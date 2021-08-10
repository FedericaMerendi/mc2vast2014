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
      <l-polyline
          ref='polyline'
          :color="polyline.color"
          :lat-lngs="latLngLine(dataGPS)"
      />
      <!--<l-marker :key="index"
                v-for="(c,index) in gps"
                :lat-lng="latLng(c.lat, c.long)"
      /> -->
    </l-map>

  </div>

</template>

<script>
//import L from 'leaflet';
import { LMap, LPolyline, LGeoJson } from 'vue2-leaflet';

export default {
  name: "AbilaMap",
  props: {
    dataGPS: Array,
  },
  components: {
    LMap,
    LPolyline,
    LGeoJson,
  },
  data() {
    return {
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 13,
      center: [36.0700, 24.8670],
      geojson: null,
      polyline: {
        color: 'green',
      },
    };
  },
  methods: {
    latLngLine: function(gps) {
      var coor = []
      for (var i = 0; i < gps.length; i++) {
        //if (gps[i].carID == car) {
        var arr = [gps[i].lat,gps[i].long]
        coor.push(arr);
        //}
      }
      return coor;
    },
  },
  computed: {
    options() {
      return {
        onEachFeature: this.onEachFeatureFunction
      };
    },
    styleFunction() {
     // const fillColor = this.fillColor; // important! need touch fillColor in computed for re-calculate when change fillColor
      return () => {
        return {
          weight: 1,
          color: "orange",
          opacity: 1,
          pane: 'mapPane',
        };
      };
    },
    onEachFeatureFunction() {
      if (!this.enableTooltip) {
        return () => {};
      }
      return (feature, layer) => {
        layer.bindTooltip(
            "<div>" + feature.properties.FENAME + ' ' +
            feature.properties.FETYPE +
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
  height: 500px;
  width: 100%;
}
</style>