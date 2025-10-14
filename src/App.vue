<template>
  <div v-cloak>
    <div class="container">
      <CountdownTimer :end-date="END_ISO" :end-message="END_MESSAGE" />
    </div>
  </div>
</template>

<script setup lang="ts">
import CountdownTimer from '@/components/CountdownTimer.vue'

const END_ISO = import.meta.env.VITE_END_ISO
const END_MESSAGE = import.meta.env.VITE_END_MESSAGE
</script>

<style scoped>
[v-cloak] {
  display: none !important;
}
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
