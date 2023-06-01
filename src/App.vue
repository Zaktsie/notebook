<template>
  <div id="app">
      <Notebook @change-page="changePage" @new-page="newPage" :pages="pages" :activePage="index" />
      <Page @save-page="savePage" @delete-page="deletePage" :page="pages[index]" />
  </div>
</template>

<script>
import Notebook from './components/Notebook'
import Page from './components/Page'

export default {
  name: 'App',
  components: {
    Notebook,
    Page
  },
  data: () => ({
    pages: [],
    index: 0
  }),
  methods: {
    newPage () { 
      this.pages.push({
        title: "",
        content: ""
      })
      this.index = this.pages.length - 1
    },
    changepage (index) {
      this.index = index
    },
    savepage () {
      
    },
    deletepage () {
      this.pages.splice(this.index, 1)
      this.index =  Math.max(0, this.index - 1)
    }
  }

}
</script>

<style>
html, body, #app {
    height: 100%;
}

body {
    margin: 0;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    display: flex;
    flex-direction: row;
}
</style>