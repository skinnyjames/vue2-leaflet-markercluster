<template>
  <v-map ref="stuff" :zoom=10 :center="initialLocation">
    <v-icondefault></v-icondefault>
    <v-tilelayer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></v-tilelayer>
    <v-marker-cluster :bulk="true" :options="clusterOptions" @l-clusterclick="click()">
      <v-marker v-for="(l, index) in locations" :key="index" :lat-lng="l.latlng" :icon="icon">
      </v-marker>
    </v-marker-cluster>
  </v-map>
</template>

<script>
  import Vue2Leaflet from 'vue2-leaflet'
  import Vue2LeafletMarkercluster from '../src/Vue2LeafletMarkercluster'
  import iconUrl from 'leaflet/dist/images/marker-icon.png'
  import shadowUrl from 'leaflet/dist/images/marker-shadow.png'
  import randomCoordinates from 'random-coordinates'

  function rand(n) {
    let max = n + 30
    let min = n - 30
    return Math.random() * (max - min) + min;
  }

  export default {
    components: {
      'v-map': Vue2Leaflet.Map,
      'v-tilelayer': Vue2Leaflet.TileLayer,
      'v-icondefault': Vue2Leaflet.IconDefault,
      'v-marker': Vue2Leaflet.Marker,
      'v-popup': Vue2Leaflet.Popup,
      'v-marker-cluster': Vue2LeafletMarkercluster
    },
    methods: {
      click: function (e) {
        console.log(this.$refs.stuff)
        console.log('cluster-event', e)
        alert("clusterclick - adding 500 more")
        this.addMore()
      },
      addMore: function() {
        console.log(this.locations.length)
        for (let i=0; i<500; i++) {
          let [lat,lng] = randomCoordinates().split(',')
          this.locations.push({
            latlng: window.L.latLng(lat,lng),
            text: 'Hola next' + i
          })
        }
        console.log(this.locations.length)
      }
    },
    data () {
      let locations = []
      let i = 1000
      while(--i){
        let [lat,lng] = randomCoordinates().split(',')
        locations.push({
          latlng: window.L.latLng(lat,lng),
          text: 'Hola ' + i
        })
      }

      let icon = window.L.divIcon({ html: '<div style="border-radius:50%;margin:0;width:20px;height:20px;background-color:blue;left:-5px;top:-5px;position:relative;"></div>'})
      return {
        locations: locations,
        icon,
        // IMPORTANT, set chunkLoading to speedup load when using bulk
        clusterOptions: { chunkedLoading: true },
        initialLocation: window.L.latLng(-34.9205, -57.953646)
      }
    },
    mounted() {
      setTimeout(() => {
        console.log('done')
        this.$nextTick(() =>{
          this.clusterOptions = { disableClusteringAtZoom: 11, removeOutsideVisibleBounds: true }
        });
      }, 5000);
    }
  }
</script>

<style module>
  html, body {
    height: 100%
  }
</style>
