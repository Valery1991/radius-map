<template>
  <div class="relative h-[100dvh] w-[100dvw]">
    <div class="absolute bottom-3 left-2 pr-3 w-full md:w-auto flex flex-col gap-3 z-50">
      <div class="flex flex-col gap-1">
        <p class="text-black font-bold">Radius (m)</p>
        <input
          v-model="radiusVal"
          type="number"
          min="0"
          max="1000"
          placeholder="Enter radius in meters"
          class="w-fit rounded-lg p-2 text-black shadow-md"
        />
      </div>

      <div class="flex flex-row gap-3">
        <input
          v-model="locationInput"
          id="locationInput"
          placeholder="Enter address, plus code, or coordinates..."
          class="w-7/12 md:w-96 rounded-lg p-2 text-black shadow-md"
          @keyup.enter="locate"
          @blur="scrollToTop"
        />
        <button
          @click="locate"
          class="rounded-lg py-2 px-4 shadow-md bg-[#fcba03] text-black font-bold"
        >
          Locate
        </button>
      </div>
    </div>

    <div ref="mapContainer" id="map" class="w-full h-full"></div>
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
    const radiusVal = ref(600)
    const locationInput = ref('')

    watch(radiusVal, (newVal) => {
      if (circle) circle.setRadius(Number(newVal))
    })

    const placeMarkerAndDrawCircle = (location: google.maps.LatLngLiteral) => {
      if (marker) marker.setMap(null) // remove previous marker

      marker = new google.maps.Marker({
        position: location,
        map: map
      })

      if (circle) circle.setMap(null) // remove previous circle

      circle = new google.maps.Circle({
        map: map,
        center: marker.getPosition()!,
        radius: Number(radiusVal.value)
      })

      map.setCenter(marker.getPosition()!)
      if (map.getZoom() < 15) map.setZoom(15)
    }

    const scrollToTop = () => {
      document.getElementById('locationInput')?.blur()
      document.getElementById('map')?.scrollIntoView({ block: 'start', behavior: 'smooth' })
    }

    const locate = async () => {
      const geocoder = new google.maps.Geocoder()

      if (isValidCoordinates(locationInput.value)) {
        const [lat, lng] = locationInput.value.split(',').map((coord) => parseFloat(coord.trim()))
        map.setCenter({ lat, lng })
        placeMarkerAndDrawCircle({ lat, lng })
        return
      }

      geocoder.geocode({ address: locationInput.value }, (results, status) => {
        if (status === 'OK') {
          const location = results[0].geometry.location
          map.setCenter(location)
          placeMarkerAndDrawCircle({ lat: location.lat(), lng: location.lng() })
        } else {
          alert('Geocode was not successful for the following reason: ' + status)
        }
      })

      scrollToTop()
    }

    const isValidCoordinates = (input: string) => {
      const coords = input.split(',')
      return coords.length === 2 && !isNaN(parseFloat(coords[0])) && !isNaN(parseFloat(coords[1]))
    }

    onMounted(async () => {
      const mapDiv = document.getElementById('map')
      const loader = new Loader({
        apiKey: import.meta.env.VITE_APP_GOOGLE_MAPS_API_KEY,
        version: 'weekly',
        libraries: ['places'] // needed for geocoder
      })

      await loader.importLibrary('core')

      if (mapDiv) {
        map = new google.maps.Map(mapDiv, {
          center: { lat: 36.9463059, lng: -4.2870067 }, // currently set to southern Spain but feel free to adjust to whatever you want, or utilize user's current location
          zoom: 10,
          streetViewControl: false
        })
        map.addListener('click', (event) => {
          placeMarkerAndDrawCircle(event.latLng.toJSON())
        })
      }
    })

    return {
      radiusVal,
      locationInput,
      scrollToTop,
      locate
    }
  }
})
</script>
