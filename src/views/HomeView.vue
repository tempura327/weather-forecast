<template>
  <div>
    <SearchBar @searchClick="search" placeholder="輸入城市查看天氣預報" className="mb-4"></SearchBar>

    <div class="flex justify-around">
      <Spinner v-show="isLoading"></Spinner>
      <BarChart :datas="datas" :setting="chartSetting.bar" v-show="!isLoading"></BarChart>
      
      <div class="flex flex-wrap w-2/4" v-show="!isLoading">
        <PieChart v-for="(data, index) in pieChartData.res" :datas="data" :title="pieChartData.title[index]" :setting="chartSetting.pie" :key="index"></PieChart>
      </div>
    </div>

  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

import SearchBar from '@/components/SearchBar.vue';
import BarChart from '@/components/BarChart.vue';
import PieChart from '@/components/PieChart.vue';
import Spinner from '@/components/Spinner.vue';

import {latLng, weatherData} from '@/types/index';

@Component({
  components:{
    SearchBar,
    BarChart,
    PieChart,
    Spinner
  }
})
export default class HomeView extends Vue {
  // data
  key = 'fec0e7e281da4c7d714ff7a5f6e37a40';
  chartSetting = {
    bar:{width:700, height:750, padding:20, gridScale:5},
    pie:{width:370,height:370,padding:10},
  }
  datas:weatherData[] = [];
  isError = false;
  isLoading = false;
  pieChartData:{res:{key:string; value:number}[][], title:string[]} = {res:[], title:[]};

  // hook

  // method
  async search(cityName:string):Promise<void>{ // taipei 台北
    this.isLoading = true;
    this.isError = false;
    try{
      this.datas = await this.getWeather(await this.getLatLng(cityName));
    }catch{
      this.isError = true;
    }finally{
      this.pieChartData = this.assembleData();
      
      this.isLoading = false;
    }
  }
  assembleData():{res:{key:string; value:number}[][], title:string[]}{
    let dateArr = [...new Set(this.datas.map(i => i.dt_txt.slice(0, 10)))];
    dateArr = dateArr.slice(1, 5);

    const res:{key:string; value:number}[][] = [];

    for(let i = 0; i < dateArr.length; i++){
      const data = this.datas.filter(d => d.dt_txt.startsWith(dateArr[i]));
      const humidityArr = data.map(d => d.main.humidity.toString());
      
      const map:{[key:string]:number} = {};
      const resArr = [];
      

      humidityArr.forEach(d => {
        if(!(d in map)){
          map[d] = 1;
        }else{
          map[d]++;
        }
      })

      for(let key in map){
        resArr.push({
          key,
          value:map[key],
        });
      }
      
      res.push(resArr);
    }

    return {
      res,
      title:dateArr,
    };
  }
  async getLatLng(cityName:string):Promise<latLng>{
    const res = await fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${cityName}&limit=1&appid=${this.key}`)
                .then((d) => d.json());

    const {lat, lon} = res[0];

    return {lat, lon};
  }
  async getWeather(data:latLng):Promise<weatherData[]>{
    const res = await fetch(`https://api.openweathermap.org/data/2.5/forecast?units=metric&lat=${data.lat}&lon=${data.lon}&appid=${this.key}`)
                .then((d) => d.json());

    return res.list;
  }
}
</script>