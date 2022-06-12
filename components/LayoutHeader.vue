<template>
  <header>
    <div id="header">
      <div id="logo">
        <a href="./index.html"
          ><img src="~assets/images/logo.png" alt="Bookshelf"
        /></a>
      </div>
      <a href="#" class="add-button" @click="callCompAMethod">
        <i class="fas fa-plus-circle" aria-hidden="true"></i>
        書籍の登録
      </a>
      <button @click="signout" class="btn btn-primary logout-button">
        ログアウト
      </button>
    </div>
  </header>
</template>
<script>
import { getAuth, signOut } from 'firebase/auth'
import EventBus from '../assets/js/event-bus'
export default {
  name: 'LayoutHeader',
  methods: {
    callCompAMethod() {
      EventBus.$emit('pass-event')
    },
    // ログアウトmethod
    signout() {
      const auth = getAuth()
      signOut(auth)
        .then(() => {
          // Sign-out successful.
          this.$store.commit('logout')
          this.$router.push({ path: '/' })
        })
        .catch((error) => {
          // An error happened.
          console.error(error)
        })
    },
  },
}
</script>

<style scoped>
.clearfix::after {
  content: '';
  clear: both;
  display: block;
}

#header {
  display: flex;
  width: 1200px;
  height: 60px;
  margin: 0 auto;
  justify-content: space-between;
  align-items: center;
  background-color: white;
}

#logo {
  display: inline-block;
}
.add-button {
  text-decoration: none;
  color: #28a745;
  font-size: 18px;
}
.add-button:hover {
  text-decoration: none;
  color: #1f8737;
}
.add-button:focus {
  text-decoration: none;
}
</style>
