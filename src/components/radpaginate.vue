<template>
  <div class = "bg-gray-900 flex justify-center py-2">
      <button class = "w-24 bg-gray-800 m-2 p-2 text-white text-sm" @click="startIndex = startDecrement(startIndex); endIndex = endDecrement(endIndex)">&#8592</button>
      <button v-for="(post,index) in Math.ceil(paginationLength/perPage)" 
      :key="post.index" 
      @click="goTo(index)">
      <p class = "w-12 bg-gray-800 m-2 p-2 text-white text-sm">{{index+1}}</p></button>
      <button class = "w-24 bg-gray-800 m-2 p-2 text-white text-sm" @click="startIndex = startIncrement(startIndex); endIndex = endIncrement(endIndex);printFirst()">&#8594</button>
  </div>
</template>

<script>
export default {

  name: 'radpaginate',

  data() {
    return {
     perPage:9,
    }
  },

  props: {
    posts: Object,
    startIndex:Number,
    endIndex:Number,
    paginationLength:Number
  },

  methods: {
    goTo(index) {
    let shift = index + 1
    let indexStart = (shift * 9) - 9
    let indexEnd = shift * 9
    let indexArray = [indexStart,indexEnd]
    this.$emit('pageChange', indexArray)
    }
  },

}
</script>

