<template>
  <div class="group" :class="className">
    <slot name="input-group-prepend" class="group_prepend"></slot>

    <slot></slot>

    <slot name="input-group-append" class="group_append"></slot>
  </div>
</template>

<script lang="ts">
  import { Component, Vue, Prop } from 'vue-property-decorator';

  @Component
  export default class InputGroup extends Vue {
    // props
    @Prop({ default: '' }) className?: string;
    // data
    // hook
    // methods
  }
</script>