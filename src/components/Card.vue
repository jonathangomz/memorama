<template>
  <div class="card">
    <img class="image" v-bind:src="image" alt="Card Image">
  </div>
</template>

<script>
export default {
  props: ['image']
}
</script>

<style scoped>
.card {
  width: 100%;
  height: 100px;
  border: 1px solid black;
  overflow: hidden;
}

.image {
  max-height: 100%;
  width: auto;
}
</style>