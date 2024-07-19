<template>
  <v-container>
    <v-card>
      <v-card-title>Camera</v-card-title>
      <v-card-subtitle>
        <v-btn @click="startFrontCamera">Start Front Camera</v-btn>
        <v-btn @click="startRearCamera">Start Rear Camera</v-btn>
        <v-btn @click="stopCamera" v-if="isCameraActive">Stop Camera</v-btn>
      </v-card-subtitle>
      <v-card-actions>
        <video ref="video" autoplay playsinline></video>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script setup>
import { ref } from 'vue'

const isCameraActive = ref(false)
const videoElement = ref(null)
const currentStream = ref(null)

const startCamera = async (facingMode) => {
  if (isCameraActive.value) {
    stopCamera()
  }

  try {
    const constraints = {
      video: {
        facingMode: facingMode,
        width: { ideal: 1280 },
        height: { ideal: 720 }
      }
    }

    const stream = await navigator.mediaDevices.getUserMedia(constraints)
    if (videoElement.value) {
      videoElement.value.srcObject = stream
      videoElement.value.play()
      isCameraActive.value = true
      currentStream.value = stream
    }
  } catch (error) {
    console.error('Error accessing the camera:', error)
    alert('Error accessing the camera. Please make sure you have granted camera permissions.')
  }
}

const startFrontCamera = async () => {
  await startCamera('user')
}

const startRearCamera = async () => {
  await startCamera('environment')
}

const stopCamera = () => {
  if (currentStream.value) {
    const tracks = currentStream.value.getTracks()
    tracks.forEach(track => track.stop())
    currentStream.value = null
  }
  if (videoElement.value) {
    videoElement.value.srcObject = null
  }
  isCameraActive.value = false
}
</script>

<style scoped>
video {
  width: 100%;
  height: auto;
}
</style>
