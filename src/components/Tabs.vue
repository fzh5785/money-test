<template>
  <ul class="tabs" :class="{[classPrefix+'-tabs']:classPrefix}">
    <li v-for="item in dataSource" :key="item.value"
        :class="liClass(item)"
        class="tabs-item"
        @click="select(item)">{{item.text}}
    </li>
  </ul>
</template>

<script lang="ts">
  import Vue from 'vue';
  import {Component, Prop} from 'vue-property-decorator';

  type DataSourceItem = {
    text: string;
    value: string;
  }
  @Component
  export default class Tabs extends Vue {
    @Prop({required: true, type: Array})
    dataSource!: DataSourceItem[];
    @Prop(String)
    readonly value!: string;
    @Prop(String)
    classPrefix?: string;

    liClass(item: DataSourceItem) {
      return {
        [this.classPrefix + '-tabs-item']: this.classPrefix,
        selected: item.value === this.value
      };
    }

    select(item: DataSourceItem) {
      this.$emit('update:value', item.value);
    }
  }
</script>

<style lang="scss" scoped>
  .tabs {
    background: rgb(243,243,243);
    display: flex;
    text-align: center;
    font-size: 24px;

    &-item {
      width: 50%;
      height: 56px;
      display: flex;
      justify-content: center;
      align-items: center;
      position: relative;

      &.selected {
        color: white;
        background: rgb(80, 131, 255);
      }

      /*&.selected::after {*/
      /*  content: '';*/
      /*  position: absolute;*/
      /*  bottom: 0;*/
      /*  left: 0;*/
      /*  border: 2px solid rgb(80, 131, 255);*/
      /*  width: 100%;*/
      /*}*/
    }
  }
</style>