<template>
<div class = "w-full lg:w-3/4 flex flex-col">
 <div class = "grid grid-cols-1 lg:grid-cols-2 xl:grid-cols-3 gap-8 p-8 lg:h-screen">
    <div class = "p-6 bg-slate-900 border-t-8 border-indigo-600 drop-shadow-lg" v-for="post in filteredPosts.slice(startIndex,endIndex)" :key="post.id">
      <h3 class = "p-2 font-bold text-2xl text-white">📡 {{post.name}}</h3>
      <p class = "p-2 text-white text-lg"><span class = "font-bold">ID:</span> {{post.id}}</p>
      <p class = "p-2 text-white text-lg"><span class = "font-bold">Lat/Long: </span> 
      <a class = "underline text-sky-500 text-lg" :href = "'https://www.google.com/maps/search/?api=1&query='+post.long+','+post.lat">{{post.lat}},{{post.long}}</a>
      </p>
      <p class = "p-2 text-white text-lg"><span class = "font-bold">Elevation:</span> {{post.elevation}}</p>
      <p class = "p-2 text-white text-lg"><span class = "font-bold">Time Zone:</span> {{post.timezone}}</p>
    </div>
  </div>
  <radpaginate :startIndex = "startIndex" :endIndex = "endIndex" :posts = "posts" :paginationLength = "paginationLength" @pageChange = indexSet />
</div>
</template>

<script>
import radpaginate from '../components/radpaginate.vue'

export default {

  name: 'radgrid',

  components: {
    radpaginate
  },

  props: {
    posts: Object,
    search: String,
    filter: String,
  },

   methods: {
     indexSet(indexArray) {
       this.startIndex = indexArray[0]
       this.endIndex = indexArray[1]
     }
   },

  data() {
    return {
     startIndex:0,
     endIndex:9,
     paginationLength:0
    }
  },

  computed: {
    //Naive Filtered Search - replace with Fuse.JS if there is time. 
    filteredPosts(value) {
           let x = this.posts.filter(p => {
            return (p.name.toLowerCase().indexOf(this.search.toLowerCase()) != -1) && (p.timezone.indexOf(this.filter) != -1);
          });
          this.paginationLength = x.length;
          return x;
    }
  }

}
</script>
