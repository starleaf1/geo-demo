<template>
  <div class="mb-2" v-if="isLocationSupported">
    <div v-if="!err">
      <v-card title="Coordinates">
        <v-card-item v-if="location">
          <div class="d-flex flex-row justify-space-between">
            <div>
              <p class="font-weight-bold">Latitude</p>
              {{ location?.coords?.latitude }}
            </div>
            <div class="ml-2">
              <p class="font-weight-bold">Longitude</p>
              {{ location?.coords?.longitude }}
            </div>
          </div>
        </v-card-item>
        <v-card-item v-else-if="isTracking">
          <v-progress-circular indeterminate size="64" color="primary"></v-progress-circular>
        </v-card-item>
        <v-card-item v-else>
          <div class="text-medium-emphasis">
            Click the button below to obtain your location
          </div>
        </v-card-item>
      </v-card>
    </div>
    <v-card v-else color="danger">
      <v-card-title>Error</v-card-title>
    </v-card>
  </div>
  <div v-else class="text-medium-emphasis mb-2">
    Geolocation is not supported by your browser
  </div>
  <v-btn v-if="!isTracking" @click="startTracking" :disabled="!isLocationSupported" color="primary" prepend-icon="mdi-crosshairs-gps">Track me</v-btn>
  <v-btn v-else @click="stopTracking" prepend-icon="mdi-stop">Stop tracking</v-btn>
</template>

<script setup>
import { ref } from 'vue';
import { onMounted } from 'vue';

const isLocationSupported = ref();
const location = ref();
const isTracking = ref(false);

const trackerId = ref();
const err = ref();

onMounted(() => {
  isLocationSupported.value = ("geolocation" in navigator);
});

const startTracking = () => {
  isTracking.value = true;
  trackerId.value = navigator.geolocation.watchPosition(
    (position) => {
      location.value = position;
      console.log("[position]", position)
    },
    (error) => {
      console.log(error);
      err.value = error;
    },
    {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 1000,
    }
  );
};

const stopTracking = () => {
  isTracking.value = false;
  navigator.geolocation.clearWatch(trackerId.value);
};

</script>
