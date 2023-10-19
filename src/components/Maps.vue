<template>
  <div>
    <div ref="mapContainer" id="map"></div>
    <input v-model="radius" placeholder="Enter radius in meters" />
    <button @click="drawCircle">Draw Circle</button>
  </div>
</template>

<script lang="ts">
import '@googlemaps/js-api-loader'
import { defineComponent, ref } from 'vue'
import { Loader } from '@googlemaps/js-api-loader'

export default defineComponent({
  name: 'GoogleMap',
  setup() {
    const radius = ref(0)
    const mapContainer = ref<HTMLElement | null>(null)
    let map: google.maps.Map
    let circle: google.maps.Circle

    const initializeMap = async () => {
      const loader = new Loader({
        apiKey: process.env.VUE_APP_GOOGLE_MAPS_API_KEY,
        version: 'weekly'
      })
      await loader.load()

      map = new google.maps.Map(mapContainer.value!, {
        center: { lat: -34.397, lng: 150.644 },
        zoom: 8
      })
    }

    const drawCircle = () => {
      if (circle) circle.setMap(null) // Remove the existing circle if any

      circle = new google.maps.Circle({
        map,
        center: map.getCenter(),
        radius: Number(radius.value)
      })
    }

    initializeMap()

    return {
      radius,
      drawCircle
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
