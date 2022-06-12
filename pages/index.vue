<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-sm-10 col-md-8 col-lg-6">
        <form id="login-form">
          <div class="form-group login__email">
            <label for="login-email" class="col-form-label">
              メールアドレス
            </label>
            <div>
              <input
                v-model="emailAddress"
                type="email"
                class="form-control"
                required
              />
            </div>
          </div>
          <div class="form-group login__password">
            <label for="login-password" class="col-form-label">
              パスワード
            </label>
            <div>
              <input
                v-model="password"
                type="password"
                class="form-control"
                required
              />
            </div>
          </div>
          <!-- <div id="login__help" class="alert alert-danger"></div> -->
          <div class="form-group login__submit">
            <div>
              <button type="button" class="btn btn-success" @click="SignUp">
                新規登録
              </button>
              <button type="button" class="btn btn-primary" @click="SignIn">
                ログイン
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import {
  getAuth,
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
} from 'firebase/auth'

export default {
  data() {
    return {
      emailAddress: '',
      password: '',
    }
  },
  methods: {
    // 新規登録methods
    SignUp() {
      const auth = getAuth()
      createUserWithEmailAndPassword(auth, this.emailAddress, this.password)
        .then((userCredential) => {
          this.$store.commit('login')
          this.$router.push({ path: '/main' })
        })
        .catch((error) => {
          alert(error.message)
          console.error(error)
        })
    },
    // ログインmethods
    SignIn() {
      try {
        const auth = getAuth()
        signInWithEmailAndPassword(auth, this.emailAddress, this.password)
          .then((user) => {
            this.$store.commit('login')
            this.$router.push({ path: '/main' })
          })
          .catch((error) => {
            console.error(error)
            alert(
              'パスワードまたはメールアドレスが間違っているためログインできません'
            )
          })
      } catch (e) {
        console.error(e)
      }
    },
  },
}
</script>
<style scoped>
.container {
  margin-top: 10%;
}
</style>