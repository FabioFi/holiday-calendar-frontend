<template>
  <div class="container">
    <h1>Holiday Calendar</h1>
    <Calendar />
  </div>
</template>

<script setup>
import Calendar from './components/Calendar.vue'
</script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #222;
  color: #fff;
}

.container {
  max-width: 600px;
  margin: 40px auto;
  background: #333;
  padding: 2rem;
  border-radius: 10px;
  box-shadow: 0 2px 8px #0002;
}

h1 {
  text-align: center;
  margin-bottom: 2rem;
}
</style>