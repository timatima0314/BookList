<template>
  <layout-wrapper>
    <layout-header />
    <layout-cover />
    <layout-book-list
      v-for="(item, index) in bookList"
      :key="item.key"
      :items="item"
      :index="index"
      v-on:child-event="deleteMethod"
      :uid="userId"
    />
    <layout-book-modal
      v-on:child-event="addMethod"
      :uid="userId"
      :list="bookList"
      :perm="Permission"
      v-on:per-event="permissionMethod"
    />
  </layout-wrapper>
</template>
<script>
import { getAuth, onAuthStateChanged } from 'firebase/auth'
import { getFirestore, doc, getDoc } from 'firebase/firestore'
export default {
  data() {
    return {
      bookList: [],
      userId: '',
      Permission: true,
    }
  },
  middleware: 'authenticated',
  async created() {
    // 非同期処理でauthMethod実行後、addGetを実行
    await this.authMethod()
    this.addGet()
  },
  methods: {
    // data().Permissionをtrueにするmethod
    permissionMethod(boolean) {
      this.Permission = boolean
    },
    // bookListにbooktitleを追加するmethod
    addMethod(val) {
      this.bookList.push(val)
    },
    // bookListから該当のtitleを削除するmethod
    deleteMethod(val) {
      const deletVal = val
      const result = this.bookList.filter((item) => {
        return item.title !== deletVal
      })
      this.bookList = result
    },
    // data().userIdにuidを代入
    async authMethod() {
      const auth = getAuth()
      await onAuthStateChanged(auth, (user) => {
        if (user) {
          const uid = user.uid
          this.userId = uid
        } else {
          // User is signed out
          // ...
        }
      })
    },
    // bookListを描写するmethod
    async addGet() {
      const db = getFirestore()
      const docSnap = await getDoc(doc(db, 'users', this.userId))
      if (docSnap.exists()) {
        const items = docSnap.data().items
        this.bookList = items
      } else {
        console.log('No such document!')
        this.Permission = false
      }
    },
  },
}
</script>
