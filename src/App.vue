<template>
  <div id="map" style="width: 100%; height: 100vh"></div>
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  name: "App",
  data() {
    return {
      map: null,
      marker: {},
      ws: null,
    };
  },
  mounted() {
    // Initialize Mapbox
    mapboxgl.accessToken =
      "pk.eyJ1IjoibWFwYm94LW1hcms1MTQiLCJhIjoiY21iZzJwc2NiMjdkZDJrcXVoZGFubWk5OSJ9.61kbg5Fbhh-00bKynkMCzg"; // Replace with your Mapbox token
    this.map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/streets-v11",
      center: [13.404954, 52.520008], // Starting position [lng, lat] (Berlin, Germany)
      zoom: 10, // Starting zoom level
    });

    // Connect to WebSocket
    this.ws = new WebSocket(
      "ws://a8b4e004beea54eb584c8fe34ebe115e-386379857.ap-southeast-1.elb.amazonaws.com/ws"
    ); // Adjust for Kubernetes service port
    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data);

      // Loop through each runner's data
      data.forEach((runner) => {
        const deviceId = runner.device?.deviceId;
        const { lat, lng } = runner.current;

        // // Skip if no valid coordinates
        if (!lat || !lng) return;

        // Update or create marker
        if (this.marker?.[deviceId]) {
          // Update existing marker position
          this.marker[deviceId].setLngLat([lng, lat]);
        } else {
          // Create new marker
          this.marker[deviceId] = new mapboxgl.Marker()
            .setLngLat([lng, lat])
            .setPopup(new mapboxgl.Popup().setText(`Runner ${runner.name}`))
            .addTo(this.map);
        }
      });
    };
  },
  beforeUnmount() {
    if (this.ws) {
      this.ws.close();
    }
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
</style>
