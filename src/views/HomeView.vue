<template>
  <div class="">
    <SearchBar @searchClick="search" className="mb-4"></SearchBar>

    <BarChart :datas="datas"></BarChart>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

import SearchBar from '@/components/SearchBar.vue';
import BarChart from '@/components/BarChart.vue';

import {latLng, weatherData} from '@/types/index';

@Component({
  components:{
    SearchBar,
    BarChart,
  }
})
export default class HomeView extends Vue {
  // data
  key = 'fec0e7e281da4c7d714ff7a5f6e37a40';
  datas = [{"dt":1664355600,"main":{"temp":303.56,"feels_like":309.75,"temp_min":302.7,"temp_max":303.56,"pressure":1006,"sea_level":1006,"grnd_level":1005,"humidity":72,"temp_kf":0.86},"weather":[{"id":803,"main":"Clouds","description":"broken clouds","icon":"04d"}],"clouds":{"all":75},"wind":{"speed":2.58,"deg":262,"gust":2.04},"visibility":10000,"pop":0.12,"sys":{"pod":"d"},"dt_txt":"2022-09-28 09:00:00"},{"dt":1664366400,"main":{"temp":303.1,"feels_like":308.31,"temp_min":302.18,"temp_max":303.1,"pressure":1007,"sea_level":1007,"grnd_level":1008,"humidity":71,"temp_kf":0.92},"weather":[{"id":803,"main":"Clouds","description":"broken clouds","icon":"04n"}],"clouds":{"all":57},"wind":{"speed":2.42,"deg":253,"gust":2.25},"visibility":10000,"pop":0.13,"sys":{"pod":"n"},"dt_txt":"2022-09-28 12:00:00"},
  {"dt":1664377200,"main":{"temp":302.51,"feels_like":306.68,"temp_min":301.98,"temp_max":302.51,"pressure":1009,"sea_level":1009,"grnd_level":1009,"humidity":70,"temp_kf":0.53},"weather":[{"id":803,"main":"Clouds","description":"broken clouds","icon":"04n"}],"clouds":{"all":74},"wind":{"speed":2.08,"deg":225,"gust":1.99},"visibility":10000,"pop":0.04,"sys":{"pod":"n"},"dt_txt":"2022-09-28 15:00:00"},{"dt":1664388000,"main":{"temp":301.43,"feels_like":304.82,"temp_min":301.43,"temp_max":301.43,"pressure":1009,"sea_level":1009,"grnd_level":1008,"humidity":73,"temp_kf":0},"weather":[{"id":803,"main":"Clouds","description":"broken clouds","icon":"04n"}],"clouds":{"all":58},"wind":{"speed":2,"deg":286,"gust":1.78},"visibility":10000,"pop":0.04,"sys":{"pod":"n"},"dt_txt":"2022-09-28 18:00:00"}]

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

    // console.log(res.list);
    // console.log(JSON.stringify(res.list));
    return res.list;
  }
  
}
</script>