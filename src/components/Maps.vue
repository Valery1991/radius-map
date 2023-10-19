<template>
  <div>
    <div ref="mapContainer" id="map"></div>
    <input
      v-model="radiusVal"
      type="number"
      min="0"
      max="1000"
      placeholder="Enter radius in meters"
    />
  </div>
</template>

<script lang="ts">
/// <reference types="googlemaps" />
import { Loader } from '@googlemaps/js-api-loader'
import { defineComponent, ref, onMounted, watch } from 'vue'

let map: google.maps.Map
let marker: google.maps.Marker | null = null
let circle: google.maps.Circle | undefined

export default defineComponent({
  name: 'GoogleMap',

  setup() {
    const radiusVal = ref(0)

    watch(radiusVal, (newVal) => {
      if (circle) circle.setRadius(Number(newVal))
    })

    const placeMarkerAndDrawCircle = (event: google.maps.MapMouseEvent) => {
      if (marker) marker.setMap(null)

      marker = new google.maps.Marker({
        position: event.latLng,
        map: map
      })

      if (circle) circle.setMap(null)

      circle = new google.maps.Circle({
        map: map,
        center: marker.getPosition()!,
        radius: Number(radiusVal.value)
      })
    }

    onMounted(async () => {
      const mapDiv = document.getElementById('map')
      const loader = new Loader({
        apiKey: import.meta.env.VITE_APP_GOOGLE_MAPS_API_KEY,
        version: 'weekly'
      })

      await loader.importLibrary('core')

      if (mapDiv)
        map = new google.maps.Map(mapDiv, {
          center: { lat: 36.9463059, lng: -4.2870067 },
          zoom: 10,
          streetViewControl: false
        })
      map.addListener('click', placeMarkerAndDrawCircle)
    })

    return {
      radiusVal
    }
  }
})
</script>

<style scoped>
#map {
  width: 100vw;
  height: 80vh;
}
</style>
