<template>
  <div id="app">
      <Notebook @change-page="changePage" @new-page="newPage" :pages="pages" :activePage="index" />
      <Page @save-page="savePage" @delete-page="deletePage" :page="pages[index]" />
  </div>
</template>

<script>
import Notebook from './components/Notebook'
import Page from './components/Page'
import Firebase from 'firebase'

const database = Firebase.initializeApp({
  apiKey: 'AIzaSyArRMEBKqsQS098-90zacO6ph1052wl-qU',
  authDomain: 'zak-notepad.firebaseapp.com',
  databaseURL: 'https://zak-notepad-default-rtdb.firebaseio.com/',
  projectId: 'zak-notepad',
  storageBucket: 'zak-notepad.appspot.com',
  messagingSenderId: '617209198520'
  }).database().ref();


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
      const page = this.pages[this.index]
      if(page.ref) {
        this.updateExistingPage(page)
      } else {
        this.insertNewpage(page)
      }
      
    },
    deletepage () {
      const ref = this.pages[this.index].ref
      ref && ref.remove()
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