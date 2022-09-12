<template>
<div class = "flex flex-col items-center bg-slate-700 w-full lg:w-1/4 drop-shadow-md">
    <div class = "scale-50 flex flex-row items-center mt-20 mb-4">
        <img class = "bg-indigo-600 p-4 rounded-lg drop-shadow-lg mr-5" src = "../assets/beetle.png">
        <h2 class = "text-7xl text-white tracking-wide"><span class = "font-semibold">Radar</span>Beetle</h2>
    </div>
        <radsearch  @searchChange="passSearch"/>
    <div class = "flex flex-row flex-wrap mt-7 mb-12 justify-center">
      <button v-for="timezone in uniqueTimeZones" :key="timezone.id" class = "bg-gray-800 m-2 p-2 text-white text-xs font-light" @click = "timezoneSelect(timezone)">{{timezone}}</button>
    </div>
</div>
</template>

<script>
import radsearch from '../components/radsearch.vue'

export default {
  name: 'radnav',

  components: {
    radsearch
  },

    props: {
    posts: Object,
    selected: String
  },

  methods: {
    passSearch(value) {
      this.$emit('searchChange', value)
    },

    timezoneSelect(value) {
      this.$emit('timezoneSelect', value)
    }
  },

  computed: {
    //Compute Filter Options From Unique TimeZones in Array
    uniqueTimeZones() {
    let unique = [...new Set(this.posts.map(item => item.timezone))]; 
    return unique
    }
  }

}
</script>
