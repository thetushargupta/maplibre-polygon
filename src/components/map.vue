<script setup lang="ts">
import { onMounted } from "vue";
import "maplibre-gl/dist/maplibre-gl.css";
import maplibregl from "maplibre-gl";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";

const defaultCoords = {
  lat: 25.198219,
  lng: 55.279792,
};

onMounted(async () => {
  const map: any = new maplibregl.Map({
    container: "map",
    style:
      "https://api.maptiler.com/maps/streets-v2/style.json?key=Wsytqqzjlw8eejm6lW3O",
    center: defaultCoords,
    zoom: 10,
    minZoom: 6,
    maxZoom: 15,
  });

  map.addControl(
    new maplibregl.NavigationControl({
      visualizePitch: true,
    }),
    "bottom-right"
  );

  // gives you your location
  map.addControl(
    new maplibregl.GeolocateControl({
      positionOptions: {
        enableHighAccuracy: true,
      },
      trackUserLocation: true,
    })
  );

  // marker
  const marker = new maplibregl.Marker({
    color: "#047857",
    draggable: true,
  })
    .setLngLat([55.279792, 25.198219])
    .addTo(map);

  // dynamic marker location function
  map.on("drag", () => {
    console.log("A dragend event occurred.");
    const point = map.getCenter();
    console.log(point);
    marker.setLngLat(point);
  });

  //mapbox draw

  const draw: any = new MapboxDraw({
    userProperties: true,
    displayControlsDefault: false,
    controls: {
      point: true,
      line_string: true,
      polygon: true,
      trash: true,
    },
  });

  // const draw2 = new MapboxDraw({
  //   userProperties: true,
  // });

  console.log("draw obj,,,,,");
  console.log(draw);

  map.addControl(draw);

  map.on("load", function () {
    map.on("draw.add", (e: any) => console.log("add...", e.features));
    map.on("draw.update", (e: any) => console.log("update...", e.features));
    map.on("draw.delete", (e: any) => console.log("delete...", e.features));
  });
});
</script>

<template>
  <div id="map"></div>
</template>

<style scoped>
.calculation-box {
  height: 75px;
  width: 150px;
  position: absolute;
  bottom: 40px;
  left: 10px;
  background-color: rgba(255, 255, 255, 0.9);
  padding: 15px;
  text-align: center;
}

p {
  font-family: "Open Sans";
  margin: 0;
  font-size: 13px;
}
</style>
