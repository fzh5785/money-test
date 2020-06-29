<template>
  <div class="nav-wrapper">
    <div class="content">
      money.vue
    </div>
    <Nav/>
  </div>
</template>

<script lang="ts">
  export default {
    name: 'money',
  };
</script>

<style lang="scss" scoped>
  .nav-wrapper {
    display: flex;
    border: 1px solid green;
    flex-direction: column;
    height: 100vh;
  }
  .content {
    overflow: auto;
    flex-grow: 1;
  }
</style>