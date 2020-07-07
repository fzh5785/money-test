<template>
  <Layout>
    <div>
      <Icon name="left"/>
      <span>编辑标签</span>
      <Notes field-name="便签名" placeholder="请输入标签名"/>
    </div>
  </Layout>
</template>

<script lang="ts">
  import Vue from 'vue';
  import {Component} from 'vue-property-decorator';
  import Notes from '@/components/Money/Notes.vue';
  @Component({
    components: {Notes}
  })
  export default class EditLabel extends Vue {

  }
</script>

<style lang="scss" scoped>

</style>