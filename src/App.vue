<template>
 <div v-if = "!errorState" class = "flex flex-col lg:flex-row h-full">
  <radnav :posts = "posts" @searchChange="setSearch" @timezoneSelect="timezoneSelected"/>
  <radgrid :posts = "posts" :search = "search" :filter = "filter"/>
 </div>

 <div v-else class = "flex flex-col h-full content-center justify-center ml-24">
  <p class = "text-white text-4xl block mb-5">Something went wrong, please try again later!</p>
  <p class = "text-red-600 text-2xl block">'{{this.errorState}}'</p>
 </div>
</template>

<script setup>
import radnav from './components/radnav.vue'
import radgrid from './components/radgrid.vue'
import axios from 'axios';
</script>

<script>

export default {

data() {
    return {
     posts:[],
     search:"",
     userLat:0,
     userLong:0,
     filter:"",
     errorState:""
    }
  },

  methods: {

    //Naive Haversine Formula - Note: Literal Distance, Rather Than Driving Distance -- Reasonable Approximation. For lower margin of error, Vincenty might be more appropriate but more complex to implement.
    getDistance(lat1, lon1, lat2, lon2) {
      var R = 6371; // Earth's Radius
      var dLat = (lat2-lat1)  * (Math.PI/180);
      var dLon = (lon2-lon1)  * (Math.PI/180); 
      var a = Math.sin(dLat/2) * Math.sin(dLat/2) + Math.cos(lat1  * (Math.PI/180)) * Math.cos(lat2  * (Math.PI/180)) * Math.sin(dLon/2) * Math.sin(dLon/2); 
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
      var d = R * c; // Distance in km
      return d; //Output in Kilometers
    },

    timezoneSelected(value) {
      this.filter = value;
      console.log(this.filter)
    },

    setSearch(value) {
      this.search = value;
    },

    timezoneAssign (index) {
     axios.get('http://vip.timezonedb.com/v2.1/get-time-zone?key=RAK3A8SEE0Z4&format=json&by=position&lat='+this.posts[index].long+'&lng='+this.posts[index].lat).then((response) => {
     this.posts[index].timezone = response.data.zoneName
      }).catch((error) => {
          this.errorState = "Failed to Assign Time Zones!"
          })
    },

    userLocate() {
        axios.get('http://ip-api.com/json').then((response) => {
        let payload = response.data;
        this.userLat = payload.lon;
        this.userLong = payload.lat;
      }).catch((error) => {
        this.errorState = "Failed to Retrieve User Location!"
})
    },

    dataStructuring() {

     axios
      .get('https://api.weather.gov/radar/stations').then((response) => {   
       let payload = response.data.features
        for(var i = 0; i < 30; i++) {
          let post = {
            name: payload[i].properties.name,
            id: payload[i].properties.id,
            lat: payload[i].geometry.coordinates[0],
            long:payload[i].geometry.coordinates[1],
            elevation: payload[i].properties.elevation.value,
            userDelta:0,
            timezone:"Calculating.."
          }
          this.posts.push(post)
        }

        //Set Distance From User on Each Array Entry
        for(var i = 0; i < this.posts.length; i++) {
          this.posts[i].userDelta = this.getDistance(this.posts[i].lat,this.posts[i].long,this.userLat,this.userLong)
        }

        //Sort by Delta in Kilometers
        this.posts = this.posts.sort((a, b) => a.userDelta-b.userDelta);
        })
      
        .catch((error) => {
          this.errorState = "Failed to Retrieve Radar Data!"
          })
        
        .finally(() => {
        //Assign Timezones to Each Entry
          for (var i = 0; i < 30; i++) {
          this.timezoneAssign(i)
          }
      })
    },

   },
  
   mounted() {

    //Establish User Lat/Long
     this.userLocate()

    //Radar Stations Payload from Weather.Gov and Timezone Establishment
     this.dataStructuring()

  },


}

</script>

