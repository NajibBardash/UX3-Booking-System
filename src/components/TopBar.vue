<script setup>

defineProps({
  msg: {
    type: String,
    required: true,
  },
})
</script>

<template>
  <div class="topBar">
    <h1 class="main-header">{{ msg }}</h1>
  </div>
</template>

<style scoped>
  .topBar {
    background-color: rgba(139, 157, 134, 1);

  }
</style>
