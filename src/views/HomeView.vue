<template>
  <div class="p-4">
    <SearchBar @searchClick="search"></SearchBar>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

import SearchBar from '@/components/SearchBar.vue';

declare type latLng = { lat: number; lon: number; };
declare type weatherData = {
  clouds:{all:number};
  dt:number;
  dt_txt:string;
  main:{
    feels_like:number;
    grnd_level:number;
    humidity:number;
    pressure:number;
    sea_level:number;
    temp:number;
    temp_kf:number;
    temp_max:number;
    temp_min:number;
  };
  pop:number;
  sys:{pod:string};
  visibility:number;
  weather:{id: number; main: string; description: string; icon: string}[];
  wind:{speed:number; deg:number; gust:number;};  
}

@Component({
  components:{
    SearchBar,
  }
})
export default class HomeView extends Vue {
  // data
  key = 'fec0e7e281da4c7d714ff7a5f6e37a40';

  // hook


  // method
  async search(cityName:string):Promise<void>{ // taipei 台北
    await this.getWeather(await this.getLatLng(cityName));
  }
  async getLatLng(cityName:string):Promise<latLng>{
    const res = await fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${cityName}&limit=1&appid=${this.key}`)
                .then((d) => d.json());

    const {lat, lon} = res[0];

    return {lat, lon};
  }
  async getWeather(data:latLng):Promise<weatherData[]>{
    const res = await fetch(`https://api.openweathermap.org/data/2.5/forecast?lat=${data.lat}&lon=${data.lon}&appid=${this.key}`)
                .then((d) => d.json());

    return res.list;
  }
  
}
</script>