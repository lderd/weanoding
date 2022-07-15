<template>
  <div v-loading="loading">
    <p>로딩중입니다 잠시만 기다려 주세요</p>
    <p>{{ loading }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loading: true
    }
  }
}
</script>

<style>

</style>
