<script setup lang="ts">
import { onMounted, ref } from "vue";
import maplibregl from "maplibre-gl";
import "maplibre-gl/dist/maplibre-gl.css";
import MapboxDraw from "@mapbox/mapbox-gl-draw";
import "@mapbox/mapbox-gl-draw/dist/mapbox-gl-draw.css";

const defaultCoords = {
  lat: 25.198219,
  lng: 55.279792,
};

const geoJsondata = ref<any>("");

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

  // zoom in and zoom out controls

  map.addControl(
    new maplibregl.NavigationControl({
      visualizePitch: true,
    }),
    "bottom-right"
  );

  // gives you your location

  const userlocation = new maplibregl.GeolocateControl({
    positionOptions: {
      enableHighAccuracy: true,
    },
    trackUserLocation: true,
  });

  // map.addControl(userlocation);

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

  // control box added
  map.addControl(draw);

  map.on("load", function () {
    map.on("draw.add", (e: any) => console.log("add...", e.features));
    map.on("draw.update", (e: any) => console.log("update...", e.features));
    map.on("draw.delete", (e: any) => console.log("delete...", e.features));
    userlocation.trigger();
  });

  // save geojson data
  const save = document.getElementById("save") as HTMLElement;

  save.addEventListener("click", function () {
    let data = draw.getAll();
    console.log(JSON.stringify(data));
    geoJsondata.value = JSON.stringify(data);
    console.log("latitude= ", userlocation._userLocationDotMarker._lngLat.lat);
    console.log("longitude= ", userlocation._userLocationDotMarker._lngLat.lng);
  });

  const todubai = document.getElementById("dubai") as HTMLElement;

  todubai.addEventListener("click", function () {
    marker.setLngLat(defaultCoords);

    map.flyTo({
      center: defaultCoords,
      zoom: 14,
      speed: 2,
      curve: 1,
    });
  });

  const mylocation = document.getElementById("mylocation") as HTMLElement;

  mylocation.addEventListener("click", function () {
    // const myCoords = {
    //   lng: userlocation._userLocationDotMarker._lngLat.lng,
    //   lat: userlocation._userLocationDotMarker._lngLat.lat,
    // };

    console.log(userlocation);

    // marker.setLngLat(myCoords);

    // map.flyTo({
    //   center: myCoords,
    //   zoom: 14,
    //   speed: 2,
    //   curve: 1,
    // });
  });
});
</script>

<template>
  <div class="relative">
    <div id="map"></div>
    <div class="absolute bottom-2 left-2 space-x-2">
      <button class="btn btn-primary min-h-0 h-7" id="save">save</button>
      <button class="btn btn-primary min-h-0 h-7" id="dubai">Dubai</button>
      <button class="btn btn-primary min-h-0 h-7" id="mylocation">
        MyLocation
      </button>
    </div>
  </div>

  <div>{{ geoJsondata }}</div>
</template>

<style scoped></style>
