<template>
  <div class="about">
    <h1><Camera /></h1>
  </div>
</template>
<script>
import Camera from '../components/Camera.vue'

export default {
  components: {
    Camera
  }
}
</script>
<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
