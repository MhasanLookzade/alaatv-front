<template>
  <div class="content-search-vue">
    <!--    searchLoading : {{ searchLoading }}-->
    mobileMode : {{ mobileMode }}
    <div class="row  main-content content-body">
      <div
        v-if="mobileMode"
        class="mobile-mode col-12 text-center">
        <div class="advance-search-btn-on-mobile-mode q-mx-lg q-mb-md">
          <q-btn
            outline
            class="full-width advance-search-btn-text"
            color="primary"
            label="جستوجوی پیشرفته"
            icon-right="mdi-feature-search-outline"
            @click="advanceSearchModal = true"
          />
          <div v-if="false"
               class="side-bar-mobile-mode"
          >
            <div
              class="modal-dialog"
              role="document"
            >
              <div class="modal-content">
                <div class="modal-header">
                  <h2
                    class="modal-title"
                    id="exampleModalLongTitle">
                    جستوجوی پیشرفته
                  </h2>
                  <div class="btn-box">
                    <q-btn
                      class="filter-btn"
                      data-dismiss="modal"
                      @click="applyFilter=true">
                      اعمال فیلتر
                    </q-btn>
                    <q-btn
                      class="close"
                      data-dismiss="modal"
                      aria-label="Close" />
                  </div>
                </div>
                <div class="modal-body">
                  <side-bar-content
                    v-model:selectedTags="selectedTags"
                    @update:selectedTags="onFilterChange"
                    :contentFilterData="contentSearchFilterData"
                    :mobileMode="mobileMode"
                    :applyFilter="applyFilter"
                    :loading="searchLoading"
                    ref="sideBar"
                  />
                </div>
                <div class="modal-footer">
                  <q-btn
                    color="red"
                    text
                    class="close-btn"
                    data-dismiss="modal">بستن
                  </q-btn>
                  <q-btn
                    color="primary"
                    class="filter-btn"
                    data-dismiss="modal"
                    @click="applyFilter=true">
                    اعمال فیلتر
                  </q-btn>
                </div>
              </div>
            </div>
          </div>
        </div>
        <q-dialog
          persistent
          v-model="advanceSearchModal"
        >
          <q-card>
            <div class="modal-content">
              <q-card-section class="row justify-between">
                <div
                  class="advance-search-modal-title">
                  جستوجوی پیشرفته
                </div>
                <div class="btn-box">
                  <q-btn
                    unelevated
                    v-close-popup
                    class="q-mr-sm"
                    color="primary"
                    data-dismiss="modal"
                    @click="applyFilter=true">
                    اعمال فیلتر
                  </q-btn>
                  <q-btn
                    flat
                    color="red"
                    v-close-popup
                    outline
                    icon-right="mdi-close"
                    label="بستن"
                  />
                </div>
              </q-card-section>
              <q-card-section>
                <side-bar-content
                  v-model:selectedTags="selectedTags"
                  @update:selectedTags="onFilterChange"
                  :contentFilterData="contentSearchFilterData"
                  :mobileMode="mobileMode"
                  :applyFilter="applyFilter"
                  :loading="searchLoading"
                  ref="sideBar"
                />
              </q-card-section>
              <q-card-actions>
                <q-btn
                  unelevated
                  color="primary"
                  class="q-mx-sm"
                  v-close-popup
                  @click="applyFilter=true">
                  اعمال فیلتر
                </q-btn>
                <q-btn
                  color="red"
                  outline
                  v-close-popup
                  icon-right="mdi-close"
                  label="بستن"
                  flat
                />
              </q-card-actions>
            </div>
          </q-card>

        </q-dialog>
      </div>
      <div
        v-if="!mobileMode"
        class="col-md-2 col-sm-0 q-gutter-sm-x-xl q-pr-lg">
        <div class="sidebar">
          <div class="sidebar__inner">
            <side-bar-content
              v-model:selectedTags="selectedTags"
              @update:selectedTags="onFilterChange"
              :contentFilterData="contentSearchFilterData"
              :mobileMode="mobileMode"
              :applyFilter="applyFilter"
              :loading="searchLoading"
              ref="sideBar"
            />
          </div>
        </div>
      </div>
      <div class="col-md-10 col-sm-12 content-list">
        <div class="content">
          <div class="tag-loading-container">
            <div class="tags-chip-group-wrapper">
              <div
                class="flex q-mr-lg-md q-ml-lg-sm"
                v-if="selectedTags.length > 0"
              >
                <p class="tags-title">
                  تگ‌ها :
                </p>
                <div class=" tag-container">
                  <q-chip
                    v-for="(tag,index) in selectedTags"
                    :key="index"
                    outlined
                    removable
                    outline
                    color="primary"
                    @remove="removeTags(tag)"
                    class="q-ml-sm"
                  >
                    {{ tag.title }}
                  </q-chip>
                </div>
              </div>
            </div>
            <div
              v-if="noData"
              class="alert alert-warning alert-dismissible fade show error-alert"
              role="alert"
            >
              <strong>متاسفیم!</strong>با توجه به خواسته شما موردی یافت نشد.
              <button type="button"
                      class="close"
                      data-dismiss="alert"
                      aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
          </div>
          <div>
            <div
              class=" horizontal-scroller q-mb-sm">
              <q-virtual-scroll
                :items="sets.list"
                virtual-scroll-horizontal
                v-slot="{ item, index }"
                @virtual-scroll="scrollMoved"
              >
                <div
                  :key="index"
                  class="set q-mr-sm"
                >
                  <div v-if="searchLoading">
                    <div v-if="item.type === 'loading' && setLoading">
                      <q-spinner-dots color="primary"
                                      size="40px" />
                    </div>
                  </div>
                  <div v-else>
                    <set-item :data="item" />
                  </div>
                </div>
              </q-virtual-scroll>
            </div>
            <div class="vertical-scroller searchResult">
              <div class="listType">
                <q-infinite-scroll ref="contentAndProductList"
                                   @load="loadNewProductAndContent"
                                   :offset="250">
                  <specifer-type
                    v-for="(item, index) in productAndContentList"
                    :key="index"
                    :info="item"
                  />
                  <template v-if="searchLoading"
                            v-slot:loading>
                    <div class="row justify-center q-my-md">
                      <q-spinner-dots color="red"
                                      size="40px" />
                    </div>
                  </template>
                </q-infinite-scroll>
                <div class="scroll-loader"
                     v-if="canSendVideoReq || canSendProductReq"
                >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import StickySidebar from 'sticky-sidebar'
import SpeciferType from 'src/components/Widgets/Content/Search/SpeciferType'
import SideBarContent from 'src/components/Widgets/Content/Search/SideBarContent'
import SetItem from 'src/components/Widgets/Content/Search/SetItem'
import { ContentList } from 'src/models/Content'
import { ProductList } from 'src/models/Product'
import { SetList } from 'src/models/Set'
import Addresses from 'src/api/Addresses'
import FilterData from 'src/assets/js/contentSearchFilterData'

export default {
  name: 'Search',
  components: {
    SetItem,
    SpeciferType,
    SideBarContent
  },
  data: () => ({
    setLoading: false,
    advanceSearchModal: false,
    slider: null,
    stopReq: true,
    sets: new SetList(),
    products: new ProductList(),
    contents: new ContentList(),
    productAndContentList: [],
    contentSearchFilterData: {},
    canSendVideoReq: true,
    canSendProductReq: true,
    canSendSetsReq: true,
    searchLoading: false,
    new_url: '',
    selectedTags: [],
    q_param: [],
    sizeOfScreen: null,
    applyFilter: false,
    backData: null,
    contentSearchApi: null
  }),
  computed: {
    noData () {
      return !this.canSendSetsReq && !this.canSendVideoReq && !this.canSendProductReq && this.sets.list.length === 0 && this.productAndContentList.length === 0
    },
    mobileMode () {
      return this.$store.getters['AppLayout/windowSize'].x <= 1024
    }
  },
  created () {
    this.setInitData()
    this.convertFilterData()
    this.getUrlParams()
    this.updateNewUrl()
    this.getPageData()
  },
  mounted () {
    if (!this.mobileMode) {
      this.setSideBarSticky()
    }
  },
  methods: {
    setInitData () {
      this.contentSearchApi = Addresses.content.search
      this.backData = FilterData
    },

    getPageData () {
      let url = this.contentSearchApi
      if (this.new_url && this.new_url.length > 2) {
        url = url.concat(this.new_url)
      }
      this.searchLoading = true
      const that = this
      that.$axios.get(url)
        .then(res => {
          const sets = res.data.data.sets
          const products = res.data.data.products
          const videos = res.data.data.videos
          this.loadSets(sets)
          const arr = [{ count: 4, key: 'contents', response: videos }, { count: 1, key: 'products', response: products }]
          arr.forEach((data) => {
            const responseData = data.response
            let oldList = {}
            if (data.key === 'products') {
              oldList = that.products
            }
            if (data.key === 'contents') {
              oldList = that.contents
            }
            if (responseData === null) {
              if (data.key === 'contents') this.canSendVideoReq = false
              else this.canSendProductReq = false
            }
            that.loadItemFromResponse(responseData, oldList, data.key)
            that.resetLists(data, oldList)
          })
          this.stopReq = false
          this.searchLoading = false
        })
        .catch(() => {
          this.stopReq = false
          this.searchLoading = false
        }
        )
    },

    setSideBarSticky () {
      this.slider = new StickySidebar('.sidebar', {
        topSpacing: 100,
        bottomSpacing: 0,
        resizeSensor: true,
        containerSelector: '.main-content',
        innerWrapperSelector: '.sidebar__inner'
      })
    },

    scrollMoved (data) {
      if (data.direction === 'decrease') return
      const lastElementIndex = data.ref.items.length - 1
      const currentElementIndex = data.index
      if (currentElementIndex === lastElementIndex && !this.setLoading && this.canSendSetsReq) {
        // this.loadMoreData()
        this.chargeSet()
        // console.log('load more----------------------------------------------------------------')
      }
    },

    convertFilterData () {
      this.contentSearchFilterData = {
        nezam: {
          options: [],
          isSearchable: false,
          title: 'نظام آموزشی'
        },
        allMaghta: {
          options: [],
          isSearchable: false,
          title: 'مقطع'
        },
        major: {
          options: [],
          isSearchable: false,
          title: 'رشته'
        },
        allLessons: {
          options: [],
          isSearchable: true,
          title: 'درس'
        },
        lessonTeacher: {
          options: [],
          isSearchable: true,
          title: 'دبیران'
        }
      }
      const keyArray = ['nezam', 'allMaghta', 'major', 'allLessons', 'lessonTeacher']
      keyArray.forEach((key) => {
        if (key === 'lessonTeacher') {
          this.backData[key]['همه_دروس'].forEach((item, index) => {
            this.contentSearchFilterData[key].options.push(
              {
                active: false,
                value: item.value,
                title: item.firstName,
                order: index
              }
            )
          })
        } else {
          this.backData[key].forEach((item, index) => {
            this.contentSearchFilterData[key].options.push(
              {
                active: false,
                value: item.value,
                title: item.name,
                order: index
              }
            )
          })
        }
      })
    },

    getNormalizedQueryTags () {
      let tags = []
      const routQuery = this.$route.query['tags[]']
      if (Array.isArray(routQuery)) {
        tags = routQuery
      } else if (typeof routQuery === 'string') {
        tags.push(routQuery)
      } else {
        tags = []
      }

      return tags
    },

    getUrlParams () {
      this.selectedTags = this.getNormalizedQueryTags().map(item => {
        return {
          active: true,
          value: item,
          title: item.replace('_', ' '),
          order: 1
        }
      })
    },

    onFilterChange () {
      this.searchLoading = true
      this.setTagsOnAddressBare()
      this.updateNewUrl()
      this.resetPageContent()
    },

    setTagsOnAddressBare () {
      const tags = {
        'tags[]': this.selectedTags.map(item => item.value)
      }
      this.$router.push({ name: 'User.Content.Search', query: tags })
    },

    updateNewUrl () {
      const tags = []
      this.selectedTags.forEach((tag, index) => {
        tags.push('tags[]=' + encodeURIComponent(tag.value))
      })
      this.new_url = '?' + tags.join('&')
    },

    resetPageContent () {
      this.clearPageContent()
      this.$refs.contentAndProductList.trigger()
      this.chargeSet()
    },

    clearPageContent () {
      this.products = new ProductList()
      this.contents = new ContentList()
      this.sets = new SetList()
      this.applyFilter = false
      this.canSendVideoReq = true
      this.canSendProductReq = true
      this.canSendSetsReq = true
      this.productAndContentList = []
    },

    removeTags (tag) {
      this.searchLoading = true
      this.updateSelectedTags(tag)
      this.updateNewUrl()
      this.resetPageContent()
    },

    updateSelectedTags (tag) {
      tag.active = false
      this.selectedTags = this.selectedTags.filter(tag => tag.active)
    },

    async chargeSet () {
      try {
        const data = await this.getItems(this.sets, 'sets')
        if (data) this.loadSets(data)
        else this.canSendSetsReq = false
        this.searchLoading = false
      } catch (err) {
        this.searchLoading = false
      }
    },

    loadSets (data) {
      if (!data) {
        return
      }
      data.data.forEach(responseItem => {
        this.sets.list.unshift(responseItem)
        // const lastElementIndex = this.sets.list.length - 1
        // this.sets.list[lastElementIndex][type] = 'loading'
        this.$nextTick(() => {
          if (window.imageObserver) window.imageObserver.observe()
        })
      })
      this.sets.paginate = { links: data.links, meta: data.meta }
      // this.loadItemFromResponse(data, this.sets, 'sets')
      if (data.data.length < 20) {
        this.$nextTick(() => {
          this.canSendSetsReq = false
        })
      }
    },

    loadNewProductAndContent (index, done) {
      // if (!this.searchLoading) {
      //   return
      // }
      this.chargeProductAndContentList(index, done)
    },

    chargeProductAndContentList (index, done) {
      const promises = []
      if (this.canSendVideoReq) {
        const contentsPromise = this.chargeItems(this.contents, 'contents', 4)
        promises.push(contentsPromise)
      }
      if (this.canSendProductReq) {
        const productsPromise = this.chargeItems(this.products, 'products', 1)
        promises.push(productsPromise)
      }
      const that = this
      Promise.all(promises)
        .then((responseDataArray) => {
          this.searchLoading = false
          done()
          // console.log('done')
          responseDataArray.forEach((data) => {
            const responseData = data.response
            let oldList = {}
            if (data.key === 'products') {
              oldList = that.products
            }
            if (data.key === 'contents') {
              oldList = that.contents
            }
            if (responseData === null) {
              if (data.key === 'contents') this.canSendVideoReq = false
              else this.canSendProductReq = false
            }
            that.loadItemFromResponse(responseData, oldList, data.key)
            that.resetLists(data, oldList)
            if (!this.mobileMode) {
              this.slider.updateSticky()
            }
          })
        })
        .catch(errors => {
          this.searchLoading = false
          console.log('-------------', errors)
        })
    },

    resetLists (data, oldList) {
      let count = data.count
      if (data.status && data.status === 'noMorePage' && oldList.list.length < count) {
        count = oldList.list.length
        if (count === 0) {
          if (data.key === 'contents') this.canSendVideoReq = false
          else this.canSendProductReq = false
          return
        }
      }
      const sample = JSON.parse(JSON.stringify(oldList.list))
      sample.splice(count)
      sample.forEach(item => this.productAndContentList.push(item))
      oldList.list.splice(0, count)
      this.$nextTick(() => {
        if (window.imageObserver) window.imageObserver.observe()
      })
    },

    chargeItems (items, key, count) {
      const that = this
      return new Promise(function (resolve, reject) {
        const remainCount = items.list.length

        if (items.paginate && items.paginate.links && items.paginate.links.prev && !items.paginate.links.next) {
          resolve({ status: 'noMorePage', key, count })
          return
        }
        if (remainCount > (count + 1)) {
          resolve({ status: 'remainCount > 10', key, count })
          return
        }
        that.getItems(items, key)
          .then(response => {
            resolve({ response, count, key })
          })
          .catch(error => {
            reject(error)
          })
      })
    },

    getItems (items, key) {
      let url = this.contentSearchApi
      if (this.new_url && this.new_url.length > 2) {
        url = url.concat(this.new_url)
      }
      if (items.paginate && items.paginate.links.next) {
        url = items.paginate.links.next
      }
      // console.log('getItems url :', url)
      const that = this
      return new Promise(function (resolve, reject) {
        that.$axios.get(url)
          .then(response => {
            const responseData = response.data.data
            let newKey = key
            if (key === 'contents') {
              newKey = 'videos'
            }
            resolve(responseData[newKey])
          })
          .catch(error => {
            reject(error)
          })
      })
    },

    loadItemFromResponse (response, data, key) {
      if (!response) {
        return
      }
      response.data.forEach(responseItem => {
        responseItem.type = key
        data.addItem(responseItem)
      })
      data.paginate = { links: response.links, meta: response.meta }
    }

  }
}
</script>

<style lang="scss" scoped>
.content-search-vue{
  .tags-title{
    font-size: 18px;
    font-weight: 500;
    @media only screen and (max-width: 1024px){
      font-size: 16px;
    }
  }
  .tag-container{
    max-width: 90%;
    display: flex;
    flex-wrap: nowrap;
    overflow: scroll;
  }
  .mobile-mode{
    .advance-search-btn-text{
      font-size: 16px;
      font-weight: 500;
    }
    .modal-content{
      .advance-search-modal-title{
        font-size: 18px;
        font-weight: 500;
      }
    }
  }

}

</style>
