<template>
  <div>
    <main class="w-screen h-screen">
      <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
      <div id="menu" class="">
        <input
          id="satellite-v9"
          type="radio"
          name="rtoggle"
          value="satellite"
          checked="checked"
        />
        <!-- See a list of Mapbox-hosted public styles at -->
        <!-- https://docs.mapbox.com/api/maps/styles/#mapbox-styles -->
        <label for="satellite-v9">satellite</label>
        <input id="light-v10" type="radio" name="rtoggle" value="light" />
        <label for="light-v10">light</label>
        <input id="dark-v10" type="radio" name="rtoggle" value="dark" />
        <label for="dark-v10">dark</label>
        <input id="streets-v11" type="radio" name="rtoggle" value="streets" />
        <label for="streets-v11">streets</label>
        <input id="outdoors-v11" type="radio" name="rtoggle" value="outdoors" />
        <label for="outdoors-v11">outdoors</label>
      </div>
    </main>
    <!-- <p>{{ state.data }}</p>
    <p>below data come from value variable</p>
    <p>{{ value }}</p> -->
  </div>
</template>
<script setup lang="ts">
// import "mapbox-gl/dist/mapbox-gl.css";
// import "v-mapbox/dist/v-mapbox.css";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";
import MapboxDraw from "@mapbox/mapbox-gl-draw";

mapboxgl.accessToken =
  "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
//const data: string;
const state = reactive({
  map: {
    container: "map",
    // accessToken:
    //   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    //style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    style: "mapbox://styles/mapbox/satellite-streets-v11",
    // style: "https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json",
    center: [444.04931277036667, 26.266912177018096] as number[],
    zoom: 11,
    maxZoom: 22,
  },
});

// mapboxgl.accessToken =
//   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
// var map = new mapboxgl.Map({
//   container: "map",
//   style: "mapbox://styles/mapbox/streets-v11?optimize=true",
//   center: [444.04931277036667, 26.266912177018096] as number[],
//   zoom: 11,
//   //     maxZoom: 22,
// });
var value = state.data;
// var geojson = {
//   type: "FeatureCollection",
//   features: [
//     {
//       type: "Feature",
//       properties: { title: "my home ", description: "back to home " },
//       geometry: {
//         type: "Point",
//         coordinates: [444.04931277036667, 26.266912177018096],
//       },
//     },
//     {
//       type: "Feature",
//       properties: { title: "villege", description: "uttar pradesh" },
//       geometry: {
//         type: "Point",
//         coordinates: [444.0481862425804, 26.266565820518633],
//       },
//     },
//     {
//       type: "Feature",
//       properties: { title: "market", description: "uttar pradesh." },
//       geometry: {
//         type: "Point",
//         coordinates: [444.0496963262558, 26.266450368122516],
//       },
//     },
//     {
//       type: "Feature",
//       properties: { title: "home", description: "asdfghjhjkl." },
//       geometry: {
//         type: "Point",
//         coordinates: [444.04630064964294, 26.23214630354235],
//       },
//     },
//   ],
//};
// for (let data in state.data) {
//   console.log(data);
// }

function onMapLoaded(map: mapboxgl.Map) {
  console.log("map loaded");
  // getGisData();
  console.log(state.data);
  //new mapboxgl.Marker().setLngLat([444.0463, 26.2321]).addTo(map);
  getGisData();
  async function getGisData() {
    state.data = await $fetch("http://localhost:3001/gisdata");
    console.log("data: ", state.data);
    for (const feature of state.data) {
      // create a HTML element for each feature
      const el = document.createElement("div");
      el.className = "marker";
      console.log("data from databse" + feature.location);
      // make a marker for each feature and add to the map
      new mapboxgl.Marker(el)
        .setLngLat(feature.location.coordinates)
        .addTo(map);
    }
  }

  var Draw = new MapboxDraw();

  map.addControl(Draw, "top-left");

  const layerList = document.getElementById("menu");
  const inputs = layerList.getElementsByTagName("input");
  for (const input of inputs) {
    input.onclick = (layer) => {
      const layerId = layer.target.id;
      map.setStyle("mapbox://styles/mapbox/" + layerId);
    };
  }

  polygon();
  async function polygon() {
    state.polygon = await $fetch("http://localhost:3001/gisdata/polygon");
    console.log(state.polygon);
    console.log("data: ", state.polygon);
    const featureCollection = {
      type: "geojson",
      data: {
        type: "FeatureCollection",
        features: [],
      },
    };
    featureCollection.data.features = state.polygon.map((element) => {
      return {
        type: "Feature",
        geometry: {
          type: "Polygon",
          coordinates: element.polygon.coordinates,
        },
      };
    });
    map.addSource("polygon-data", featureCollection);
    map.addLayer({
      id: "park-boundary",
      type: "fill",
      source: "polygon-data",
      paint: {
        "fill-color": "#FF0000",
        "fill-opacity": 0.4,
      },
    });
  }
}
</script>
<style>
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
.marker {
  background-image: url("http://localhost:3000/assets/images/img.jpg");
  background-size: cover;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
}
.mapboxgl-popup {
  max-width: 200px;
}
.mapboxgl-popup-content {
  text-align: center;
  font-family: "Open Sans", sans-serif;
}
#menu {
  position: absolute;
  background: #efefef;
  padding: 10px;
  font-family: "Open Sans", sans-serif;
}
</style>