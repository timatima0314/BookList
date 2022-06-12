<template>
  <client-only>
    <vue-final-modal v-model="showModal">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title">書籍の登録</h4>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
              @click="closeModal"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body m-1">
            <form id="book-form">
              <div class="form-group row">
                <label for="add-book-title" class="col-md-3 col-form-label"
                  >タイトル</label
                >
                <div class="col-md-9">
                  <input
                    id="add-book-title"
                    v-model="bookTitle"
                    type="text"
                    class="form-control"
                    required
                  />
                </div>
              </div>
              <div class="form-group row">
                <div class="col-md-3">表紙画像</div>
                <div class="col-md-9">
                  <div class="custom-file">
                    <input
                      id="add-book-image"
                      type="file"
                      accept=".jpg,.jpeg,.png,.gif, image/jpeg,image/png,image/gif"
                      class="custom-file-input"
                      @change="onImageUploaded"
                      required
                    />
                    <label
                      id="add-book-image-label"
                      class="custom-file-label"
                      for="add-book-image"
                      >{{ imageSet }}</label
                    >
                  </div>
                </div>
              </div>
              <div id="add-book__help" class="alert alert-danger"></div>
              <button
                id="submit_add_book"
                type="button"
                class="btn btn-default btn-success btn-block"
                @click="upLoad"
              >
                保存する
              </button>
              <!-- <button @click="daun"></button> -->
            </form>
          </div>
        </div>
      </div>
    </vue-final-modal>
  </client-only>
</template>
<script>
import {
  getFirestore,
  setDoc,
  doc,
  updateDoc,
  arrayUnion,
} from 'firebase/firestore'
import { getStorage, ref, uploadBytes, getDownloadURL } from 'firebase/storage'
import EventBus from '~/assets/js/event-bus'
export default {
  name: 'LayoutBookModal',
  props: {
    uid: {
      type: String,
    },
    list: {
      type: Array,
    },
    perm: {
      type: Boolean,
    },
  },
  data() {
    return {
      bookTitle: '',
      showModal: false,
      image: null,
      imgUrl: '',
      // imageSet: imageadd(),
    }
  },
  created() {
    EventBus.$on('pass-event', () => {
      this.openModal()
    })
  },
  computed: {
    // 表紙画像form書き換え
    imageSet() {
      if (this.image === null) {
        return 'file選択'
      } else {
        return this.image.name
      }
    },
  },
  methods: {
    // Modalが開くmethod
    openModal() {
      this.showModal = true
    },
    // Modalが閉じるmethod
    closeModal() {
      this.showModal = false
      this.bookTitle = ''
    },
    // firestoreに保存するmethod
    async addTask() {
      const db = getFirestore()
      // 新規登録者ならsetDocに
      if (this.perm === false) {
        await setDoc(doc(db, 'users', this.uid), {
          items: [{ imgUrl: this.imgUrl, title: this.bookTitle }],
        })
        // 親のdata().Permissioをtrueに
        this.$emit('per-event', true)
      } else {
        const washingtonRef = doc(db, 'users', this.uid)
        const item = { title: this.bookTitle, imgUrl: this.imgUrl }
        await updateDoc(washingtonRef, {
          items: arrayUnion(item),
        })
      }
      // 親（page/index.vue）にbookTitleを送信
      this.$emit('child-event', { title: this.bookTitle, imgUrl: this.imgUrl })
      this.closeModal()
    },
    // event(=e)から画像データを取得する
    onImageUploaded(e) {
      const image = e.target.files[0]
      this.image = image
    },
    // fireStorageに画像をupするmethod
    async uploadImg() {
      const storage = getStorage()
      const storageRef = ref(storage, `${this.uid}/${this.bookTitle}`)
      await uploadBytes(storageRef, this.image).then((snapshot) => {
        console.log('Uploaded a blob or file!')
      })
      this.getimgUrl()
    },
    // fireStrageに保存した画像のurlを所得するmethod
    getimgUrl() {
      const storage = getStorage()
      const gsReference = ref(
        storage,
        `gs://booklist-12dec.appspot.com/${this.uid}/${this.bookTitle}`
      )
      getDownloadURL(gsReference)
        .then((url) => {
          this.imgUrl = url
          this.addTask()
        })
        .catch((err) => console.log(err))
    },
    // fireStoreとfireStorageに保存するmethod
    upLoad() {
      if (this.bookTitle === '' || this.image === null) {
        alert('タイトルかfileが未登録です')
      } else {
        this.uploadImg()
      }
    },
  },
}
</script>
<style scoped>
#add-book__help {
  display: none;
}

.custom-file-label::after {
  content: '参照';
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 1s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>