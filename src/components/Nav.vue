<template>
  <div class="nav">
    <router-link to="/money" >
      <Icon name="money"/>
      记账</router-link>
    丨
    <router-link to="/labels" >
      <Icon name="label"/>
      标签</router-link>
    丨
    <router-link to="/statistics" >
      <Icon name="sta"/>
      统计</router-link>
  </div>
</template>

<script lang="ts">

  export default {
    name: 'Nav'
  };
</script>

<style lang="scss" scoped>
.nav {
  border: 1px solid red;
}
</style>