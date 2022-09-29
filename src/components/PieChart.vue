<template>
  <div class="w-2/4">
    <canvas id="chart" ref="canvas"></canvas>
  </div>
</template>

<script lang="ts">
  import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

  @Component

  export default class BarChart extends Vue {
    // prop
    @Prop() datas!:{key:string; value:number}[];
    @Prop() setting!:{width:number; height:number; padding:number;};
    @Prop() colorOption?:string[];
    @Prop({default:''}) title!:string;
    @Prop({default:true}) needLabel!:boolean;

    // data
    canvasSetting = {
      width:700,
      height:700,
      padding:20, // keep space for label,
    };
    chartSetting = {
      x:350, // width / 2
      y:350, // height / 2
      r:290, // width / 2 - padding * 3      
    };
    $refs!:{
      canvas:HTMLCanvasElement;
    };
    ctx!:CanvasRenderingContext2D;
    beginDeg = 0;
    color = ['#F92472', '#FAA23D', '#B2E64C', '#08A6BB', '#3590F3', '#054A91', '#AA7DFC', '#4b0082'];
    total = 0;
    
    // hook
    created():void{
      if(this.setting){
        // adjust the position and size of chart
        this.canvasSetting = JSON.parse(JSON.stringify(this.setting));

        this.chartSetting.x = this.setting.width / 2;
        this.chartSetting.y = this.setting.height / 2;
        this.chartSetting.r = Math.min(...[this.setting.width, this.setting.height]) / 2.5 - this.setting.padding * 2;
      }

      if(this.colorOption){
        this.color = JSON.parse(JSON.stringify(this.colorOption));
      }
    }
    mounted():void{
      this.$refs.canvas.width = this.canvasSetting.width;
      this.$refs.canvas.height = this.canvasSetting.height;
      
      this.ctx = this.$refs.canvas.getContext('2d') as CanvasRenderingContext2D;

      this.draw();
    }    

    // methods
    drawPie():void{
      this.total = this.datas.reduce((acc, cur) => {return acc + cur.value}, 0);

      for(let i = 0; i < this.datas.length; i++) {
        // value / total is degree. 
        // to convert degree to radian to * 360 * PI / 180.
        const valueDeg = this.datas[i].value / this.total * 360 * Math.PI / 180;
        const endDeg = this.beginDeg + valueDeg;

        this.ctx.beginPath();
        this.ctx.moveTo(this.chartSetting.x, this.chartSetting.y);
        this.ctx.arc(this.chartSetting.x, this.chartSetting.y, this.chartSetting.r, this.beginDeg, endDeg);
        this.ctx.fillStyle = this.color[i];
        this.ctx.fill();

        // draw a line from center to any point on radian. 
        // and draw a vertical line from the point to radian, then draw a horizontal line from center to radian.
        // you will get a triangle. the short side is textY, and the long side is textX.
        // the reason why this.chartSetting.r / n, is that I want to put the text inside the chart.
        // if you want to put the text outside the chart, and the space is enough, you can this.chartSetting.r * n.
        const textDeg = this.beginDeg + valueDeg * 0.5;
        const textX = this.chartSetting.x + (this.chartSetting.r / 1.5) * Math.cos(textDeg);
        const textY = this.chartSetting.y + (this.chartSetting.r / 1.5) * Math.sin(textDeg);

        this.ctx.font = '12px Arial';
        this.ctx.fillStyle = '#ffffff';

        this.ctx.fillText(`${(this.datas[i].value / this.total) * 100}%`, textX, textY);
        
        this.beginDeg = endDeg;

        this.ctx.restore();
      }      
    }
    putTitle():void{
      this.ctx.font = '12px Arial';
      this.ctx.fillStyle = '#333333';
      this.ctx.textAlign = 'start';
      this.ctx.fillText(`${this.title}濕度`, this.canvasSetting.padding, this.canvasSetting.padding + 12);
    }
    drawLabel():void{
      for(let i = 0; i < this.datas.length; i++){
        this.ctx.beginPath();
        this.ctx.fillStyle = this.color[i];
        // the vertical distance between labels is 14. and the size of label is 10 * 10
        this.ctx.fillRect(this.canvasSetting.padding, this.canvasSetting.height - this.canvasSetting.padding * 2 - 14 * i, 10, 10);

        this.ctx.font = '12px Arial';
        this.ctx.fillStyle = '#333333';
        this.ctx.textAlign = 'start';
        // put texts on the right side of labels. and the horizontal distance between text and label is 2.
        this.ctx.fillText(`濕${this.datas[i].key}%`, this.canvasSetting.padding * 2 + 4, this.canvasSetting.height - 10 - 14 * i);
      }
    }    
    draw():void{
      this.clearCanvas();
      this.drawPie();
      this.putTitle();

      if(this.needLabel){
        this.drawLabel();
      }
    }
    clearCanvas():void{
      this.ctx.clearRect(0, 0, this.canvasSetting.width, this.canvasSetting.height);
    }    


    // watch
    @Watch('datas', { deep: true })
    datasWatch():void{
      this.draw();
    }    
  }
</script>