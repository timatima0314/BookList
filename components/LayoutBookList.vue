<template >
  <div class="book-item">
    <div class="book-item__image-wrapper">
      <img class="book-item__image" alt="" :src="items.imgUrl" />
    </div>
    <div class="book-item__detail">
      <div class="book-item__title">{{ items.title }}</div>
      <div class="book-item__delete-wrapper">
        <button
          type="button"
          class="btn btn-danger book-item__delete"
          @click="Delet"
        >
          <i class="fas fa-trash-alt" aria-hidden="true"></i>
          削除
        </button>
      </div>
    </div>
  </div>
</template>
<script>
import { getFirestore, updateDoc, arrayRemove, doc } from 'firebase/firestore'
import { getStorage, ref, deleteObject } from 'firebase/storage'

export default {
  name: 'LayoutBookList',
  props: {
    items: {
      type: Object,
    },
    uid: {
      type: String,
    },
  },
  methods: {
    // firestoreから該当dataを削除するmethod
    async Delet() {
      const db = getFirestore()
      const washingtonRef = doc(db, 'users', this.uid)
      await updateDoc(washingtonRef, {
        items: arrayRemove({
          title: this.items.title,
          imgUrl: this.items.imgUrl,
        }),
      })
      // // 親（pages/index.vue）に削除するdata(this.docTitle)を送信
      this.$emit('child-event', this.items.title)
      this.delImg()
    },
    // firestrageから画像ファイルを削除するmethods
    delImg() {
      const storage = getStorage()
      // Create a reference to the file to delete
      const desertRef = ref(
        storage,
        `gs://booklist-12dec.appspot.com/${this.uid}/${this.items.title}`
      )
      // Delete the file
      deleteObject(desertRef)
        .then(() => {
          console.log('File deleted successfully')
        })
        .catch((error) => {
          console.log(error)
        })
    },
  },
}
</script>
<style scoped>
.book-item {
  width: 270px;
  margin: 15px;
  float: left;
  background-color: white;
  border: solid 1px #ddd;
}

.book-item__image-wrapper {
  width: 270px;
  height: 270px;
  padding: 10px;
  display: table-cell;
  text-align: center;
  vertical-align: middle;
}

.book-item__image {
  max-height: 250px;
  max-width: 250px;
  vertical-align: middle;
}

.book-item__detail {
  padding: 10px;
  background-color: #fafafa;
  border-top: solid 1px #ddd;
}

.book-item__title {
  margin: 0 auto;
  height: 36px;
  text-align: center;
  overflow: hidden;
}

.book-item__delete-wrapper {
  margin-top: 5px;
  text-align: right;
}

.book-item__delete {
  color: #dc3545;
  background-color: transparent;
  border-color: #dc3545;
}
.book-item__delete:hover {
  color: #fff;
  background-color: #dc3545;
  border-color: #dc3545;
}
.book-item__delete:focus {
  outline-color: #dc3545;
  box-shadow: 0 0 0 0.2rem rgba(220, 53, 69, 0.5);
}
</style>