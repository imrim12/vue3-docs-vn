<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <p>Component hiện tại: A</p>
  <span style="margin-right: 20px">count: {{ count }}</span>
  <button @click="count++">+</button>
</template>
