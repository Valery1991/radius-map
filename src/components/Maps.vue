<template>
  <div>
    <div ref="mapContainer" id="map"></div>
    <input v-model="radius" type="number" min="0" max="1000" placeholder="Enter radius in meters" />
    <button @click="drawCircle">Create circle</button>
  </div>
</template>

<script lang="ts">
// Google type wasn't being picked up otherwise...
/// <reference types="googlemaps" />
import { Loader } from '@googlemaps/js-api-loader'
import { defineComponent, ref, onMounted } from 'vue'

let map: google.maps.Map
let marker: google.maps.Marker | null = null

export default defineComponent({
  name: 'GoogleMap',
  setup() {
    const radius = ref(0)
    let circle: google.maps.Circle | undefined

    onMounted(async () => {
      const loader = new Loader({
        apiKey: import.meta.env.VITE_APP_GOOGLE_MAPS_API_KEY,
        version: 'weekly'
      })

      await loader.importLibrary('core')
    })

    const drawCircle = () => {
      // Check if a marker has been placed
      if (!marker) {
        alert('Please place a marker on the map first.')
        return
      }

      // If there's an existing circle, remove it
      if (circle) circle.setMap(null)

      // Draw a new circle centered on the marker
      circle = new google.maps.Circle({
        map: map,
        center: marker.getPosition()!,
        radius: Number(radius.value)
      })
    }

    return {
      radius,
      drawCircle
    }
  },

  mounted() {
    const mapContainer = document.getElementById('map')

    if (mapContainer) {
      map = new google.maps.Map(mapContainer, {
        center: { lat: 36.9463059, lng: -4.2870067 },
        zoom: 10,
        streetViewControl: false
      })

      map.addListener('click', (event) => {
        if (marker) marker.setMap(null)

        marker = new google.maps.Marker({
          position: event.latLng,
          map: map
        })
      })
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
