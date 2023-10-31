<script setup>
import { ref } from 'vue'
const msg = ref('')
</script>

<template>
  <p>Component hiện tại: B</p>
  <span style="margin-right: 20px">Message is: {{ msg }}</span>
  <input v-model="msg">
</template>
