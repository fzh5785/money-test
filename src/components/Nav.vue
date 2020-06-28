<template>
  <div>
    <router-link to="/money" >记账</router-link>
    丨
    <router-link to="/labels" >标签</router-link>
    丨
    <router-link to="/statistics" >统计</router-link>
  </div>
</template>

<script lang="ts">
  export default {
    name: 'Nav'
  };
</script>

<style lang="scss" scoped>

</style>