<template>
  <button @click="title = 'Changed Popup Title'">Change Title</button>
  <div id="map" />
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import { onMounted } from "vue";
import { createApp, defineComponent, ref } from "vue";
import MyPopup from "@/components/MyPopup.vue";

export default {
  setup() {
    const title = ref("Unchanged Popup Title");
    onMounted(() => {
      mapboxgl.accessToken =
        "yourAccessToken";
      const map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/light-v9",
      });

      map.on("load", () => {
        // Here we want to load a layer
        map.addSource("usa", {
          type: "geojson",
          data:
            "https://raw.githubusercontent.com/johan/world.geo.json/master/countries/USA.geo.json",
        });
        map.addLayer({
          id: "usa-fill",
          type: "fill",
          source: "usa",
          paint: {
            "fill-color": "red",
          },
        });
        // Here we want to setup the dropdown
        map.on("click", "usa-fill", function (e) {
          new mapboxgl.Popup()
            .setLngLat(e.lngLat)
            .setHTML('<div id="popup-content"></div>')
            .addTo(map);
          const MyNewPopup = defineComponent({
            extends: MyPopup,
            setup() {
              return { title };
            },
          });
          createApp(MyNewPopup).mount("#popup-content");
        });
      });
    });
    return { title };
  },
};
</script>

<style>
#map {
  height: 100vh;
}
</style>