<template>
  <div class="flex flex-col h-screen max-h-screen">
  <!--    Search /  results   -->
    <div class="flex z-20 justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32 ">
    <!--  Search Inpit    -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4 font-bold">IP Address Tracker</h1>
        <div class="flex">
          <input v-model="queryIp"
              class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none" type="text" placeholder="Search for any IP address or leave empty to get your IP info">
          <i @click="getIpInfo" class="cursor-pointer bg-black text-white px-4 rounded-tr-md rounded-br-md flex items-center fas fa-chevron-right"></i>
        </div>
      </div>
      <!-- IP INFO   -->
      <IPInfo v-if="ipInfo" :ipInfo="ipInfo"/>
    </div>

<!--Map-->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import IPInfo from "@/components/IPInfo";
import leaflet from "leaflet";
import {onMounted, ref} from "vue";
import axios from "axios";


export default {
  name: 'Home',
  components: {
    IPInfo
  },
  setup(){
    let mymap;
    const queryIp = ref("");
    const ipInfo = ref(null);
    onMounted(()=>{
      mymap = leaflet.map("mapid").setView([44.816299, 20.460384], 12);

      leaflet.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoidmVsamFqZW5hamJvbGppIiwiYSI6ImNrdGRoZmdxcjJmYnUyd245Z3pmMmluMG0ifQ.MqED6o7nJvVIXSEDnMx6WA', {
        attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        maxZoom: 18,
        id: 'mapbox/streets-v11',
        tileSize: 512,
        zoomOffset: -1,
        accessToken: 'pk.eyJ1IjoidmVsamFqZW5hamJvbGppIiwiYSI6ImNrdGRoZmdxcjJmYnUyd245Z3pmMmluMG0ifQ.MqED6o7nJvVIXSEDnMx6WA'
      }).addTo(mymap);

    })

    const getIpInfo = async ()=>{
       try{

      const data = await axios.get(`https://geo.ipify.org/api/v1?apiKey=at_y6Inf6Pc2IEiOD99YN4otYFqgGzWM&ipAddress=${queryIp.value}`);
      const result = data.data;
      ipInfo.value={
        address: result.ip,
        state: result.location.region,
        region: result.location.city,
        timezone: result.location.timezone,
        isp: result.isp,
        lat: result.location.lat,
        lng: result.location.lng
      }
     leaflet.circle([ipInfo.value.lat,ipInfo.value.lng], {
        color: 'red',
        fillColor: '#f03',
        fillOpacity: 0.5,
        radius: 1600
      }).addTo(mymap);
      mymap.setView([ipInfo.value.lat,ipInfo.value.lng], 13)
      }
      catch (err) {
        alert(err.message)
      }
    }
    return{queryIp, ipInfo, getIpInfo}
  },
}
</script>
