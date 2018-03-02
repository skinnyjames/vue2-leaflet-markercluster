<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
import L from 'leaflet'
import 'leaflet.markercluster'

export default {
  props: [
    'options',
    'bulk'
  ],
  data() {
    return {
      mapObject: L.markerClusterGroup(this.options),
      _isMounted: false
    }
  },
  watch: {
    options: function() {
      this._add()
    }
  },
  mounted () {
    let vm = this
    this._add()
  },
  beforeDestroy () {
    this._remove()
  },
  methods: {
    deferredMountedTo (parent) {
      this._isMounted = false
      if (this.bulk) {
        for (var i = 0; i < this.$children.length; i++) {
          this.$children[i].parent = this.mapObject
          for (var j = 0; j < this.$children[i].$children.length; j++) {
            if (typeof this.$children[i].$children[j].deferredMountedTo === "function") {
              this.$children[i].$children[j].deferredMountedTo(this.$children[i].mapObject);
            }
          }
        }
      }
      else {
        for (var i = 0; i < this.$children.length; i++) {
          this.$children[i].deferredMountedTo(this.mapObject)
        }
      }
      if (!parent.hasLayer(this.mapObject._featureGroup)) {
        this.mapObject.addTo(parent);
        // emit events
        [
          'clusterclick',
          'clustermouseover',
          'clustermouseout',
          'animationend',
          'spiderfied',
          'unspiderfied'
        ].forEach(eName =>
          this.mapObject.on(
            eName,
            e => this.$emit('l-' + eName, e)
          )
        )
      }
      this._isMounted = true;
    },
    _remove () {
      this.mapObject.clearLayers()
      this.parent.removeLayer(this.mapObject)
      
    },
    _add () {
      this.mapObject.clearLayers() 
      if (this.bulk) { 
        let markers = this.$children.map(marker => marker.mapObject)
        this.mapObject.addLayers(markers)
      } else {
        for (var i = 0; i < this.$children.length; i++) {
          this.mapObject.addLayer(this.$children[i].mapObject)
        }
      }
     
      if (this.$parent._isMounted) {
        this.deferredMountedTo(this.$parent.mapObject)
      }
    },
  }
}
</script>
