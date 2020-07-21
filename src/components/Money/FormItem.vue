<template>
  <div>
    <label class="formItem">
      <span class="name">{{this.fieldName}}</span>
      <input :type="type||'text'" :placeholder="placeholder"
             :value="value" @input="onValueChange($event.target.value)">
      <!--:value="value" @input="onInput" === v-model-->

    </label>
  </div>
</template>

<script lang="ts">
  import Vue from 'vue';
  import {Component, Prop} from 'vue-property-decorator';

  @Component
  export default class FormItem extends Vue {
    @Prop({default: ''}) readonly value!: string;
    @Prop({required: true}) fieldName!: string;
    @Prop() placeholder?: string;
    @Prop() type?: string;


    onValueChange(value: string) {
      this.$emit('update:value', value);
    }

    // onInput(event: KeyboardEvent){
    //   const input = event.target as HTMLInputElement
    //   this.value = input.value
    // }
  }
</script>

<style lang="scss" scoped>
  .formItem {
    font-size: 14px;
    padding-left: 16px;
    display: flex;
    align-items: center;

    .name {
      padding-right: 16px;
    }

    input {
      height: 28px;
      flex-grow: 1;
      background: transparent;
      border: none;
    }
  }
</style>