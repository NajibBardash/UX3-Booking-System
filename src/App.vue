<script setup>
import TopBar from './components/TopBar.vue'
</script>

<template>
  <header>

    <div class="wrapper">
      <TopBar msg="Svenssons Hantverk AB" />
    </div>

  </header>

  <main>

    <div>
      <p>Some text</p>
    </div>

  </main>
</template>

<style scoped>
  header {
    background-color: rgba(139, 157, 134, 1);
    border: 1px solid red;
    width: 100%;
  }

  main {
    background-color: white;
    border: 1px solid red;
  }
</style>
