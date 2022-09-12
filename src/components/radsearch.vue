<template>
<div class = "w-full flex justify-center">
 <input class = "outline-none p-2 bg-slate-600 w-2/3" type="text" v-model="searchTerm"  @keydown="onSearch" placeholder="Search..." />
</div>
</template>

<script>
export default {
  
  name: 'radsearch',

  data() {
    return {
      searchTerm: ''
    }
  },

  methods: {
    onSearch() {
      this.$emit('searchChange', this.searchTerm)
    }
  },
  
}
</script>

