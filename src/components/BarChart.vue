<template>
  <div class="w-2/4">
    <canvas id="chart" ref="canvas"></canvas>
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
    maxValue = 0; // this is used to draw grid line and to adjust height of bars.
    

    // hook
    created():void{
      this.canvasSetting = JSON.parse(JSON.stringify(this.setting));

      this.maxValue = this.getMax(this.datas.map((i:weatherData) => i.main.temp_max));
    }
    mounted():void{
      this.$refs.canvas.width = this.canvasSetting.width;
      this.$refs.canvas.height = this.canvasSetting.height;
      
      this.ctx = this.$refs.canvas.getContext('2d') as CanvasRenderingContext2D;
    }

    // methods
    drawLine(ctx:CanvasRenderingContext2D, startX:number, startY:number, endX:number, endY:number, color:string){
        ctx.save();

        ctx.strokeStyle = color;
        ctx.beginPath();
        ctx.moveTo(startX, startY);
        ctx.lineTo(endX, endY);
        ctx.stroke();
        
        ctx.restore();
    }
    drawBar(ctx:CanvasRenderingContext2D, x:number, y:number, width:number, height:number, color:string){
        ctx.save();

        ctx.fillStyle = color;
        ctx.fillRect(x, y, width, height);

        ctx.restore();
    }
    drawGridLines() {
      const canvasActualHeight = this.canvasSetting.height - this.canvasSetting.padding * 2;
   
      let gridValue = 0;

      while (gridValue <= this.maxValue) {
        const gridY = canvasActualHeight * (1 - gridValue / this.maxValue) + this.canvasSetting.padding;

        this.drawLine(
          this.ctx,
          0,
          gridY,
          this.canvasSetting.width,
          gridY,
          '#cccccc'
        );
   
        this.drawLine(
          this.ctx,
          15,
          this.canvasSetting.padding / 2,
          15,
          gridY + this.canvasSetting.padding / 2,
          '#cccccc'
        );
   
        this.ctx.save();

        this.ctx.fillStyle = '#333333';
        this.ctx.textBaseline = 'bottom';
        this.ctx.font = 'bold 12px Arial';

        this.ctx.fillText(`${gridValue.toString()}℃`, 0, gridY - 2);
        this.ctx.restore();
   
        gridValue += this.canvasSetting.gridScale;
      }
    }
    drawBars() {
      let dateArr = [...new Set(this.datas.map(i => i.dt_txt.slice(0, 10)))];
      dateArr = dateArr.slice(1, 5);

      const canvasActualHeight = this.canvasSetting.height - this.canvasSetting.padding * 2;
      const canvasActualWidth = this.canvasSetting.width - this.canvasSetting.padding * 2;
   
      const numberOfBars = dateArr.length;
      const barSize = canvasActualWidth / numberOfBars / 4; // divide a group into 4 parts. 2 for bars and 2 for blank.

      for(let i = 0; i < dateArr.length; i++){
        const data = this.datas.filter(d => d.dt_txt.startsWith(dateArr[i]));

        const maxTemp = Math.max(...data.map(d => d.main.temp_max));
        const minTemp = Math.min(...data.map(d => d.main.temp_min));
        
        const maxTempBarHeight = Math.round((canvasActualHeight * maxTemp) / this.maxValue);
        const minTempBarHeight = Math.round((canvasActualHeight * minTemp) / this.maxValue);

        this.drawBar(
          this.ctx,
          // (this.canvasSetting.padding + i * barSize) * 4,

          // if this.canvasSetting.padding not be added, scale will be covered by bar on left side. 
          // multiply 4 is because there are 4 parts in a group.
          this.canvasSetting.padding + (i * barSize) * 4 + barSize, 
          // if maxTempBarHeight not be discounted, bars will drawn from top of canvas.
          // and if this.canvasSetting.padding not be discounted, bars will over bottom of x axis. 
          this.canvasSetting.height - maxTempBarHeight - this.canvasSetting.padding,
          barSize,
          maxTempBarHeight,
          '#F92472',
        );

        this.drawBar(
          this.ctx,
          // (this.canvasSetting.padding + i * barSize) * 4 + barSize * 2,

          // if this.canvasSetting.padding not be added, scale will be covered by bar on left side. 
          // multiply 4 is because there are 4 parts in a group.          
          this.canvasSetting.padding + (i * barSize) * 4 + barSize * 2,
          // if maxTempBarHeight not be discounted, bars will drawn from top of canvas.
          // and if this.canvasSetting.padding not be discounted, bars will over bottom of x axis.           
          this.canvasSetting.height - minTempBarHeight - this.canvasSetting.padding,
          barSize,
          minTempBarHeight,
          '#08A6BB',
        ); 

        this.drwaLable(dateArr[i], this.canvasSetting.padding + (i * barSize) * 4 + barSize * 1.5);
      }
    }
    drwaLable(str:string, x:number):void{
      this.ctx.save();

      this.ctx.fillStyle = '#333333';
      this.ctx.textBaseline = 'bottom';
      this.ctx.font = 'bold 12px Arial';

      this.ctx.fillText(str, x, this.canvasSetting.height);
      this.ctx.restore();      
    }
    clearCanvas():void{
      this.ctx.clearRect(0, 0, this.canvasSetting.width, this.canvasSetting.height);
    }
    draw() {
      this.clearCanvas();
      this.drawGridLines();
      this.drawBars();
    }
    getMax(arr:number[]):number{
      return Math.max(...arr);
    }

    // watch
    @Watch('datas')
    datasWatch(newVal:weatherData[]):void{
      this.maxValue = this.getMax(newVal.map((i:weatherData) => i.main.temp_max));
      
      this.draw();
    }
  }
</script>