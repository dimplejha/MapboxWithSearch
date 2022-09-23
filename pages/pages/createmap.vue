<template>
  <main class="w-screen h-screen">
    <div id="map"></div>
    <head>
      <link
        href="https://api.mapbox.com/mapbox-gl-js/v2.10.0/mapbox-gl.css"
        rel="stylesheet"
      />
      <link
        rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v5.0.0/mapbox-gl-geocoder.css"
        type="text/css"
      />

      <v-map
        class="w-full h-full"
        :options="state.map"
        @loaded="onMapLoaded"
      ></v-map>
    </head>

    <div id="menu">
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
      <input id="light-v10" type="radio" name="rtoggle" value="light-v10" />
      <label for="light-v10">light</label>
      <input id="dark-v10" type="radio" name="rtoggle" value="dark-v10" />
      <label for="dark-v10">dark</label>
      <input id="streets-v11" type="radio" name="rtoggle" value="streets-v11" />
      <label for="streets-v11">streets</label>
      <input
        id="outdoors-v11"
        type="radio"
        name="rtoggle"
        value="outdoors-v11"
      />
      <label for="outdoors-v11">outdoors</label>
    </div>
  </main>
</template>

<script setup lang="ts">
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";

// mapboxgl.accessToken =
//   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
//function onMapLoaded() {
const state = reactive({
  map: {
    // container: "map", // container ID
    // accessToken:
    //   "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    // // Choose from Mapbox's core styles, or make your own style with Mapbox Studio
    // style: "mapbox://styles/mapbox/streets-v11?optimise=true", // style URL
    // center: [-74.5, 40], // starting position [lng, lat]
    // zoom: 9, // starting zoom
    // projection: "globe", // display the map as a 3D globe

    accessToken:
      "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    // style: "https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json",
    center: [444.04931277036667, 26.266912177018096] as number[], //uses longitude, latitude
    zoom: 6,
    maxZoom: 22,
  },
});

console.log("line nu 30");

function onMapLoaded(map: mapboxgl.Map) {
  console.log("a");
  console.log(state.map);
  map.on("style.load", () => {
    map.setFog({}); // Set the default atmosphere style
  });

  const layerList = document.getElementById("menu");
  const inputs = layerList.getElementsByTagName("input");

  for (const input of inputs) {
    input.onclick = (layer) => {
      const layerId = layer.target.id;
      map.setStyle("mapbox://styles/mapbox/" + layerId);
    };
  }

  map.addControl(
    new MapboxGeocoder({
      accessToken:
        "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
      mapboxgl: mapboxgl,
    })
  );

  map.on("load", () => {
    // Load an image from an external URL.
    map.loadImage(
      "https://docs.mapbox.com/mapbox-gl-js/assets/cat.png",
      (error, image) => {
        if (error) throw error;

        // Add the image to the map style.
        map.addImage("cat", image);

        // Add a data source containing one point feature.
        map.addSource("point", {
          type: "geojson",
          data: {
            type: "FeatureCollection",
            features: [
              {
                type: "Feature",
                geometry: {
                  type: "Point",
                  coordinates: [-77.4144, 25.0759],
                },
              },
            ],
          },
        });

        // Add a layer to use the image to represent the data.
        map.addLayer({
          id: "points",
          type: "symbol",
          source: "point", // reference the data source
          layout: {
            "icon-image": "cat", // reference the image
            "icon-size": 0.25,
          },
        });
      }
    );
  });
}
</script>


<style >
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
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

#menu {
  position: absolute;
  background: #efefef;
  padding: 10px;
  font-family: "Open Sans", sans-serif;
}
</style>


