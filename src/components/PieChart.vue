<template>
  <div class="w-1/2">
    <canvas id="chart" class="outline-gray-light"  ref="canvas"></canvas>
  </div>
</template>

<script lang="ts">
  import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

  import { weatherData } from '@/types/index';

  @Component

  export default class BarChart extends Vue {
    @Prop() datas!:weatherData[];
    @Prop({default:() => {return {width:700, height:700, padding:20, gridScale:5}}}) setting!:{width:number; height:number; padding:number; gridScale:number};

    // data
    canvasSetting = {
      width:700,
      height:700,
      padding:20, // keep space for scale
      gridScale:5,
    }
    $refs!:{
      canvas:HTMLCanvasElement;
    };
    ctx!:CanvasRenderingContext2D;
    

    // hook
    created():void{
      this.canvasSetting = JSON.parse(JSON.stringify(this.setting));


    }
    

    // methods
    


    // watch
    // @Watch('datas')
    // datasWatch(newVal:weatherData[]):void{
    
    // }
  }
</script>

<style scoped>
  .outline-gray-light{
    outline: 1px solid #AEAEAE;
  }
</style>
