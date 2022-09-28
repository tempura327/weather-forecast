<template>
  <div class="spinner" :class="`spinner-${size} text-${color} ${className}`">
    <span>Loading...</span>
  </div>
</template>

<script lang="ts">
  import { Component, Vue, Prop } from 'vue-property-decorator';

  @Component({
    components: {},
  })
  export default class Spinner extends Vue {
    // props
    @Prop({ default: 'md' }) size!: string;
    @Prop({ default: 'primary' }) color!: string;
    @Prop({ default: '' }) className!: string;
  }
</script>
