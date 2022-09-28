<template>
  <div>
    <SearchBar @searchClick="search" className="mb-4"></SearchBar>

    <div class="flex justify-center">
      <Spinner v-show="isLoading"></Spinner>
      <BarChart :datas="datas"  v-show="!isLoading"></BarChart>
      
      <!-- once PieChart is done, replace BarChart below with PieChart -->
      <BarChart :datas="datas"  v-show="!isLoading"></BarChart>
    </div>

  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

import SearchBar from '@/components/SearchBar.vue';
import BarChart from '@/components/BarChart.vue';
import Spinner from '@/components/Spinner.vue';

import {latLng, weatherData} from '@/types/index';

@Component({
  components:{
    SearchBar,
    BarChart,
    Spinner
  }
})
export default class HomeView extends Vue {
  // data
  key = 'fec0e7e281da4c7d714ff7a5f6e37a40';
  datas:weatherData[] = [];
  isError = false;
  isLoading = false;

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
      this.isLoading = false;
    }
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

    // console.log(res.list);
    // console.log(JSON.stringify(res.list));
    return res.list;
  }
}
</script>