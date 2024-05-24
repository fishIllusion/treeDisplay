<template>
  <div id="app">
    <TreeCatalogue class="root" :treeData="treeData"/>
  </div>
</template>

<script>
import TreeCatalogue from '@/components/TreeCatalogue';
import { treeData } from '@/mockData/mockdata.js'

export default {
  name: 'App',
  components: {
    TreeCatalogue
  },
  data() {
    return {
      treeData: []
    }
  },
  mounted() {
    this.treeData = treeData;
    console.log(this.treeData)
  }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.root {
  width: 300px;
  min-height: 50px;
  margin: 0 auto;
  border: 1px solid yellowgreen;
}
</style>
