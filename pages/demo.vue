<template>
  <main class="w-screen h-screen">
    <v-map
      class="w-full h-full"
      :options="data.options"
      @loaded="onMapLoaded"
    />
  </main>
</template>
<script setup lang="ts">
import mapboxgl from "mapbox-gl";
import VMap from "v-mapbox";
const data = reactive({
  options: {
    accessToken:
      "pk.eyJ1IjoibWF5dXJ3YWtpa2FyIiwiYSI6ImNsNmdjdGxwbjBiNGMzY282bWh0dng2c2kifQ.y-m4-zQKOeOOnDG5I1u6ng",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    center: [73.8567, 18.5204],
    zoom: 11,
    maxZoom: 22,
    crossSourceCollisions: false,
    failIfMajorPerformanceCaveat: false,
    attributionControl: false,
    preserveDrawingBuffer: true,
    hash: false,
    minPitch: 0,
    maxPitch: 60,
  } as mapboxgl.MapboxOptions,
});
function onMapLoaded(map: mapboxgl.Map) {
  console.log(map);
  const marker = new mapboxgl.Marker({
    draggable: true,
    color: "#AA336A",
  });
  marker.setLngLat([30.5, 50.5]);
  marker.addTo(map);
  map.on("click", (e) => {
    console.log("I am clicked", e.lngLat.lat, e.lngLat.lng);
    new mapboxgl.Marker({
      draggable: true,
      color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
    })
      .setLngLat([e.lngLat.lng, e.lngLat.lat])
      .addTo(map);
    new mapboxgl.Popup()
      .setLngLat(e.lngLat)
      .setHTML(`Country name: ${e.features[0].properties.name}`)
      .addTo(map);
  });

  //flyto method take to the provided LngLat
  map.flyTo({
    center: [30.5, 50.5],
    zoom: 9,
    speed: 0.9,
    curve: 1,
    easing(t) {
      return t;
    },
  });
}
</script>
<style>
html,
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.w-screen {
  width: 100vw;
}
.h-screen {
  height: 100vh;
}
.h-full {
  height: 100%;
}
.w-full {
  width: 100%;
}
</style>