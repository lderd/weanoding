<template>
  <div v-loading='fullscreenloading'>
      <p>로딩중입니다 잠시만 기다려 주세요</p>
      <p>{{ fullscreenloading }}</p>
      <!-- <p>잘 안보이지롱</p> -->
  </div>
</template>

<script setup>
import { ref } from 'vue'
const fullscreenloading = ref(true)
</script>

<style>

</style>
