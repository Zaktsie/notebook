<template>
  <div id="app">
    <div v-if="user">
      <button @click="logout">Logout</button>
      <Notebook @change-page="changePage" @new-page="newPage" :pages="pages" :activePage="index" />
      <Page @save-page="savePage" @delete-page="deletePage" :page="pages[index]" />
    </div>
    <div v-else>
      <button @click="login">Login with Google</button>
    </div>
  </div>
</template>


<script>
import Notebook from './components/Notebook'
import Page from './components/Page'
import Firebase from 'firebase/app'
import 'firebase/auth'
import 'firebase/database'


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
  data() {
    return {
      user: null,
      pages: [],
      index: 0
    };
  },
  mounted() {
    database.once("value", (snapshot) => {
      snapshot.forEach((page) => {
        this.pages.push({
          ref: page.ref,
          title: page.child("title").val(),
          content: page.child("content").val()
        });
      });
    });
  },
  created() {
    Firebase.auth().onAuthStateChanged((user) => {
      this.user = user;
    });
    this.fetchPages();
  },
  methods: {
    async login() {
  const provider = new Firebase.auth.GoogleAuthProvider();
  try {
    await Firebase.auth().signInWithPopup(provider);
  } catch (error) {
    if (error.code === "auth/popup-closed-by-user") {
      // Handle popup closed by user error
      console.log("Login cancelled by the user");
    } else {
      // Handle other authentication errors
      console.error(error);
    }
  }
},
    async logout() {
      try {
        await Firebase.auth().signOut();
        this.user = null;
      } catch (error) {
        console.error(error);
      }
    },
    fetchPages() {
      const userId = this.user ? this.user.uid : '';
      Firebase
        .database()
        .ref(`users/${userId}/pages`)
        .on('value', (snapshot) => {
          const data = snapshot.val();
          if (data) {
            this.pages = Object.values(data);
          } else {
            this.pages = [];
          }
        });
    },


    newPage() {
      this.pages.push({
        title: "",
        content: ""
      });
      this.index = this.pages.length - 1;
    },
    changePage(index) {
      this.index = index;
    },
    savePage() {
      const page = this.pages[this.index];
      if (page.ref) {
        this.updateExistingPage(page);
      } else {
        this.insertNewPage(page);
      }
    },
    deletePage() {
      const ref = this.pages[this.index].ref;
      if (ref) {
        ref.remove();
      }
      this.pages.splice(this.index, 1);
      this.index = Math.max(0, this.index - 1);
    },
    updateExistingPage(page) {
      page.ref.update({
        title: page.title,
        content: page.content
      });
    },
    insertNewPage(page) {
      page.ref = database.push(page);
    }
  }
};
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