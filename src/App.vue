<template>
<b-container fluid class="h-100 d-flex flex-column">
    <b-row class="bg-light py-2">
      <b-col>
        <b-row>
          <b-form-input v-model="urlImage" placeholder="Image url"></b-form-input>
        </b-row>
        <b-row>
          <b-form-input v-model="opacityImage" type="number" min="0" max="1" step="0.1"></b-form-input>
          <b-button href="https://imgbb.com/" target="_blank">Upload Image on imgbb.com</b-button>
        </b-row>
      </b-col>
      <b-col md="auto">
        <b-form-select v-model="region" :options="regionOptions" @change="onChange()"></b-form-select>
      </b-col>
      <b-col md="auto" class="d-flex">
        <b-col align-self="center" class="p-1">
          <b-form-input v-model="boundsImgW" type="number" min="-180" max="180" step="0.01" :disabled="region!=null"></b-form-input>  
        </b-col>
        <b-col class="p-1">
          <b-form-input v-model="boundsImgN" type="number" min="-90" max="90" step="0.01" :disabled="region!=null"></b-form-input>
          <b-form-input v-model="boundsImgS" type="number" min="-90" max="90" step="0.01" :disabled="region!=null"></b-form-input>
        </b-col>
        <b-col align-self="center" class="p-1">
          <b-form-input v-model="boundsImgE" type="number" min="-180" max="180" step="0.01" :disabled="region!=null"></b-form-input>
        </b-col>
      </b-col>
    </b-row>
  <b-row id="map-div" class="flex-grow-1">
    <l-map :bounds="boundsImg">
      <l-image-overlay :url="urlImage" :bounds="boundsImg" :opacity="opacityImage"/>
      <l-control-layers position="topright"></l-control-layers>
      <l-tile-layer
        v-for="tileProvider in tileProviders"
        :key="tileProvider.name"
        :name="tileProvider.name"
        :visible="tileProvider.visible"
        :url="tileProvider.url"
        :attribution="tileProvider.attribution"
        layer-type="base"/>
    </l-map>
  </b-row>
</b-container>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer, LImageOverlay, LControlLayers } from "vue2-leaflet";

export default {
  components: {
    LMap,
    LTileLayer,
    LImageOverlay,
    LControlLayers
  },
  data() {
    return {
      zoom: 0,
      center: latLng(0, 0),
      tileProviders: [
        {
          name: 'Mapbox.Streets',
          visible: true,
          url: 'https://api.mapbox.com/styles/v1/mapbox/streets-v9/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoicmFmbnVzcyIsImEiOiIzMVE1dnc0In0.3FNMKIlQ_afYktqki-6m0g',
          attribution: '',
        },
        {
          name: 'Mapbox.Satellite',
          visible: false,
          url: 'https://api.mapbox.com/styles/v1/mapbox/satellite-streets-v9/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoicmFmbnVzcyIsImEiOiIzMVE1dnc0In0.3FNMKIlQ_afYktqki-6m0g',
          attribution: '',
        },
        {
          name: 'OpenStreetMap',
          visible: false,
          attribution:'&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
          url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
        },
        {
          name: 'Esri.WorldImagery',
          visible: false,
          url: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
          attribution: 'Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
        }
      ],
      urlImage: "https://cdnfiles2.biolovision.net/www.faune-alsace.org/images/map/map.jpg",
      region: null,
      boundsImgN: 49.09,
      boundsImgS: 47.42,
      boundsImgW: 6.56,
      boundsImgE: 8.79,
      regionOptions: [
        { value: null, text: 'Enter manually'},
        { value: [49.09, 47.42, 6.56, 8.79], text: 'faune-alsace' },
      ],
      opacityImage:.5,
    };
  },
  methods: {
    onChange() {
      if (this.region){
        this.boundsImgN = this.region[0]
        this.boundsImgS = this.region[1]
        this.boundsImgW = this.region[2]
        this.boundsImgE = this.region[3]
      }
    }
  },
  computed: {
    boundsImg: function () {
      return [[this.boundsImgN,this.boundsImgE],[this.boundsImgS,this.boundsImgW]]
    }
  }
};
</script>

<style>
html, body{
  height: 100%;
  margin:0;
}
</style>
