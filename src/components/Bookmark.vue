<template>
  <q-btn
    class="bookmark-btn"
    rounded
    flat
    :loading="loading"
    @click="bookmark"
  >
    <q-tooltip
      anchor="top middle"
      self="bottom middle"
      :offset="[10, 10]"
    >
      نشان کردن
    </q-tooltip>
    <img
      :class="isFavored ? 'hide' : ''"
      class="btnFavorite-off"
      src="https://nodes.alaatv.com/upload/landing/28/modal/landing-taftan-modal-book-mark.png"
      alt="book-mark"
    >
    <svg
      class="btnFavorite-on"
      :class="isFavored ? '' : 'hide'"
      width="22"
      height="22"
      viewBox="0 0 24 24"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path d="M15.4162 2C18.678 2 20.3089 3.6472 20.3089 6.94159V18.4394C20.3089 21.6848 18.0093 22.9406 15.1879 21.2445L13.0351 19.9398C12.5459 19.6462 11.763 19.6462 11.2738 19.9398L9.12099 21.2445C6.29955 22.9406 4 21.6848 4 18.4394V6.94159C4 3.6472 5.63089 2 8.89267 2H15.4162Z"
            fill="#292D32"
            stroke="#292D32"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round" />
    </svg>
    <template v-slot:loading>
      <q-spinner
        color="primary"
      />
    </template>
  </q-btn>
</template>

<script>
export default {
  name: 'Bookmark',
  emits: [
    'update:value',
    'onLoad',
    'onError'
  ],
  props: {
    value: {
      default: false,
      type: Boolean
    },
    baseRoute: {
      default: '',
      type: String
    },
    favoredRoute: {
      default: '',
      type: String
    },
    unfavoredRoute: {
      default: '',
      type: String
    },
    size: {
      default: '',
      type: String
    }
  },
  data () {
    return {
      loading: false
      // isFavored: false
    }
  },
  computed: {
    isFavored: {
      get () {
        return this.value
      },
      set (value) {
        this.$emit('update:value', value)
      }
    }
  },
  methods: {
    bookmark () {
      this.loading = true
      if (!this.baseRoute) {
        this.bookmarkWithIndividualRoutes()
        return
      }
      this.bookmarkWithBaseRoute()
    },
    bookmarkWithBaseRoute () {
      const nextStatus = (this.isFavored) ? 'unfavored' : 'favored'
      this.$axios.post(this.baseRoute + '/' + nextStatus)
        .then((res) => {
          this.isFavored = !this.isFavored
          this.loading = false
          this.$emit('onLoad', res)
        })
        .catch((err) => {
          this.loading = false
          this.$emit('onError', err)
        })
    },
    bookmarkWithIndividualRoutes () {
      const nextStatus = (this.isFavored) ? 'unfavoredRoute' : 'favoredRoute'
      this.$axios.post(this[nextStatus])
        .then((res) => {
          this.isFavored = !this.isFavored
          this.loading = false
          this.$emit('onLoad', res)
        })
        .catch((err) => {
          this.loading = false
          this.$emit('onError', err)
        })
    }
  }
}
</script>

<style scoped lang="scss">
.bookmark-btn {
  z-index: 1000;
  padding: 5px;
  margin: 5px;
}
.hide{
  display: none;
}
.btnFavorite-off {
  width: 20px;
  height: 20px;
}

</style>
<style lang="scss"></style>
