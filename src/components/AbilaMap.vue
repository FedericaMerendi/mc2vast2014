<template>
  <div class="row map">
      <b-button
            class="map-btn"
            size="sm"
            variant="light"
            ref='btnToggle'
            @click="hideLocations">
        Hide locations
      </b-button>
      <b-button
          class="map-btn"
          size="sm"
          variant="light"
          ref='btnReset'
          @click="resetMap">
        Reset map
      </b-button>
    <l-map
        class="abila"
        :zoom="zoom"
        :minZoom="minZoom"
        :maxZoom="maxZoom"
        :maxBounds="maxBounds"
        :center="center"
        style="height: 100%; width: 100%">

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
          <l-tooltip>{{p[0].fullName}} drove this route. (PathID: {{p[0].pathID}}) </l-tooltip>
      </l-polyline>

      <div v-if="showMarkers">
          <l-marker
              v-for="(l,i) in locations"
              :key="'L'+i"
              :lat-lng="latLong(l.lat,l.long)"
              @click="getLocation(l.location)">
            <l-tooltip>{{l.location}}</l-tooltip>
          </l-marker>
      </div>

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
      showMarkers: true,
      loc: this.locations,
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 13,
      minZoom:12,
      maxZoom:18,
      bounds: latLngBounds([
        [37.1102, 25.8250],
        [	35.0408, 23.9100]
      ]),
      maxBounds: latLngBounds([
      [36.0964, 24.8191],
      [	36.0408, 24.9100]
    ]),
      center: [36.0650, 24.8670],//[36.0700, 24.8670],
      geojson: null,
      url: '/data/background.png'
    }
  },
  methods: {
    resetMap() {
      this.$emit('reset-map');
    },
    getLocation(location) {
      this.$emit('get-location', location);
    },
    hideLocations() {
      this.showMarkers = !this.showMarkers
      this.$refs.btnToggle.innerText = this.showMarkers?'Hide locations':'Show locations';
    },
    latLong(lat,lng) {
      return latLng(lat,lng)
    },

    latLngLine: function(gps) {
      let coor = []
      for (let i = 0; i < gps.length; i++) {
        let arr = [gps[i].lat, gps[i].long]
        coor.push(arr);
      }
      return coor;
    },
    randomColor() {
      let letters = '0123456789ABCDEF'.split('');
      let color = '#';
      for (let i = 0; i < 6; i++) {
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
  },
}
</script>

<style scoped>
.map{
  height: 500px;
  width: 100%;
  margin: auto auto auto auto;
}
.map-btn {
  width: 40%;
  margin:auto auto 5px auto;
}
.abila{
  border: 1px solid gainsboro;
  margin-bottom: 5px;
}
</style>