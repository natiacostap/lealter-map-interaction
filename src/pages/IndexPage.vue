<template>
  <q-page class="flex row">
    <div class="col-md-2">
      <SideBar
        :data="activeFeature?.feature.properties"
        @set-timeslot="handleTimeSlot"
        :show-floating-population="showFloatingPopulation"
      />
    </div>
    <div class="col-md-10 col-sm-12 col-xs-12">
      <div id="map" class="map-size"></div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref, watch } from "vue";
import SideBar from "src/components/SideBar.vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import maipu from "src/utils/maipu.json";

export default defineComponent({
  name: "IndexPage",
  components: {
    SideBar,
  },
  setup() {
    const map = ref(null);
    const timeSlot = ref(null);
    const currentLayer = ref(null);
    const activeFeature = ref(null);
    const showFloatingPopulation = ref(false);

    const initMap = () => {
      const view = L.map("map", {
        center: [-33.506508, -70.77356],
        zoomControl: false,
        zoom: 13,
      });

      L.tileLayer(
        "https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png",
        {
          attribution:
            '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>',
          subdomains: "abcd",
          maxZoom: 20,
        }
      ).addTo(view);

      addGeoJsonLayer(view);

      return view;
    };

    const addGeoJsonLayer = (mapInstance) => {
      const layer = L.geoJson(maipu, {
        style: timeSlot.value ? stylePopulation : basicStyle,
        onEachFeature: onEachFeature,
        filter: (feature) => {
          return timeSlot.value
            ? feature.properties.bloque_horario === timeSlot.value.value
            : feature;
        },
      });

      layer.addTo(mapInstance);
      currentLayer.value = layer;
    };

    const highlightFeature = (layer) => {
      layer.setStyle({
        weight: 5,
        color: "black",
        dashArray: "1",
      });
      layer.bringToFront();
    };

    const resetHighlight = (layer) => {
      if (timeSlot.value) {
        layer.setStyle(stylePopulation(layer.feature));
      } else {
        layer.setStyle(basicStyle);
      }
    };

    const zoomToFeature = (e) => {
      const layer = e.target;

      if (activeFeature.value) {
        resetHighlight(activeFeature.value);
      }
      if (timeSlot.value) {
        showFloatingPopulation.value = true;
      }
      activeFeature.value = layer;
      highlightFeature(layer);

      map.value.fitBounds(layer.getBounds());
    };

    const onEachFeature = (feature, layer) => {
      layer.on({
        click: zoomToFeature,
      });
    };
    const getColor = (population) => {
      if (population > 8000) return "#222A52";
      if (population > 4000) return "#383D9F";
      if (population > 2000) return "#4D54DB";
      if (population > 1000) return "#9297F8";
      if (population > 500) return "#D5DDF3";
      if (population > 100) return "#dae2f9";
      if (population > 50) return "#ebf0ff";
      return "#fff";
    };

    const basicStyle = {
      fillColor: "transparent",
      weight: 2,
      opacity: 1,
      color: "#222A52",
      dashArray: "4",
      fillOpacity: 0.5,
    };

    const stylePopulation = (feature) => {
      return {
        fillColor: getColor(feature.properties.poblacion_flotante),
        weight: 1,
        opacity: 1,
        color: "white",
        dashArray: "4",
        fillOpacity: 0.9,
      };
    };

    onMounted(() => {
      map.value = initMap();
    });

    const handleTimeSlot = (slot) => {
      timeSlot.value = slot;
    };

    watch(timeSlot, (newValue, oldValue) => {
      if (newValue?.value !== oldValue?.value) {
        if (currentLayer.value) {
          currentLayer.value.clearLayers();
          map.value.removeLayer(currentLayer.value);
        }
        addGeoJsonLayer(map.value);
        activeFeature.value = null;
        map.value.setView([-33.506508, -70.77356], 13); // Reset zoom and center
      }
    });

    return {
      map,
      activeFeature,
      handleTimeSlot,
      showFloatingPopulation,
    };
  },
});
</script>
<style>
.map-size {
  position: relative;
  width: 100%;
  height: 100%;
  min-width: 200px;
  min-height: 300px;
}
</style>
