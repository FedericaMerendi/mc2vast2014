<template>
  <div class="row map">
    <l-map
        class="abila"
        :zoom="zoom"
        :minZoom="minZoom"
        :maxZoom="maxZoom"
        :maxBounds="maxBounds"
        :center="center"
        style="height: 100%; width: 100%"
            >
      <l-image-overlay :url="url"
                       :bounds="bounds"
      pane="tilePane"></l-image-overlay>
      <l-geo-json
          v-if="show"
          :geojson="geojson"
          :options="options"
          :options-style="styleFunction"
      />
      <l-polyline
          v-for="(p,i) in this.pathsGPS"
          :key="i"
          ref='polyline'
          :color="randomColor()"
          :lat-lngs="latLngLine(p, i)"
      >
          <l-tooltip>{{p[0].fullName}}, {{p[0].pathID}}</l-tooltip>

      </l-polyline>

     <l-marker
         v-for="(l,i) in locations"
         :key="'L'+i"
         :lat-lng="latLong(l.lat,l.long)">
        <l-tooltip>{{l.location}}</l-tooltip>

     </l-marker>
    </l-map>

  </div>

</template>

<script>
import { latLngBounds, latLng } from "leaflet";
import { LMap, LImageOverlay, LGeoJson, LPolyline, LMarker,  LTooltip} from 'vue2-leaflet';

export default {
  name: "AbilaMap",
  props: {
    dataGPS: Array,
    locations: Array,
  },
  components: {
    LMap,
    LPolyline,
    LImageOverlay,
    LGeoJson,
    LMarker,
    LTooltip,
  },
  data() {
    return {
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 13,
      minZoom:13,
      maxZoom:18,
      bounds: latLngBounds([
        [37.1102, 25.8250],
        [	35.0408, 23.9100]
      ]),
      maxBounds: latLngBounds([
      [36.0964, 24.8191],
      [	36.0408, 24.9100]
    ]),
      center: [36.0700, 24.8670],
      geojson: null,
      url: '/data/background.png'
    }
  },
  methods: {
    latLong(lat,lng) {
      return latLng(lat,lng)
    },
    latLngLine: function(gps) {
      var coor = []
      for (var i = 0; i < gps.length; i++) {
        var arr = [gps[i].lat, gps[i].long]
        coor.push(arr);
      }
      return coor;
    },
    randomColor() {
      var letters = '0123456789ABCDEF'.split('');
      var color = '#';
      for (var i = 0; i < 6; i++) {
        color += letters[Math.round(Math.random() * 15)];
      }
      return color;
    },
  },
  computed: {
    pathsGPS() {
      return this.dataGPS.reduce(function (r, a) {
        r[a.pathID] = r[a.pathID] || [];
        r[a.pathID].push(a);
        return r;
      }, Object.create(null));
    },
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
          color: "#696969",
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
  height: 450px;
  width: 100%;
}

</style>