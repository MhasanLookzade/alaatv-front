<template>
  <div class="cart-item">
    <div class="title-above">{{ cartItem.product.title }}</div>
    <div class="item-info-box row justify-between">
      <div class="info-photo col-sm-2 col-xs-3">
        <q-img :ratio="1" :src="cartItem.product.photo"/>
      </div>
      <div class="info-details col-sm-7 col-xs-7">
        <div class="title">{{ cartItem.product.title }}</div>
        <div class="more-info-box">
          <!--       v-if="infoDetails[infoDetails.length - 1] && cartItem.product.attributes"-->
          <div
            v-for="(info, index) in infoDetails"
            :key="index"
            class="info"
          >
            <div v-if="info.name">
              <q-icon :name="info.icon"/>
              <span class="info-desc">{{ info.title }} {{ (info.desc)? ':' : ''}} {{ info.desc }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="info-btn-box col-sm-2 col-xs-1">
        <q-btn
          class="delete-btn"
          icon="isax:trash"
          rounded
          flat
          :loading="loading"
          @click="deleteItem"
        >
          <template v-slot:loading>
            <q-spinner
              color="primary"
            />
          </template>
        </q-btn>
        <q-btn
          class="more-btn"
          icon="isax:more-circle"
          rounded
          flat
          :loading="loading"
          @click="toggleMenu"
        >
          <template v-slot:loading>
            <q-spinner
              color="primary"
            />
          </template>
        </q-btn>
      </div>
    </div>
    <div
      v-if="hasGrand"
      class="grand-item-other-items-box row"
    >
      <div class="col-md-2 clear-space"></div>
      <div class="col-md-10 col-sm-12">
        <cart-items-grand-mode
          v-for="(item, index) in cartItem.order_product"
          :key="index"
          :cart-item="item"
        />
      </div>
    </div>
    <div class="item-detail-box row items-center">
      <div class="clear-space col-md-2"></div>
      <div class="time-receive col-sm-3 col-xs-12 q-py-xs q-pr-sm text-center">
        <q-icon class="q-mr-xs" color="primary" size="20px" name="isax:truck"/>
        <span v-if="hasGrand">زمان دریافت: {{cartItem.order_product[0].product.attributes.info.download_date[0]}}</span>
        <span v-else>زمان دریافت: {{cartItem.product.attributes.info.download_date[0]}}</span>
      </div>
      <div class="row col-md-7 col-sm-8 col-xs-12 justify-end">
        <div class="price-detail q-mr-xl">
          <div class="discount flex  justify-between ">
            <div class="flex items-center">
              <div class="discount-percent q-ml-md text-white q-pa-xs">{{discount}}%</div>
              <div class="price-base q-ml-sm">{{cartItem.price.base}}</div>
            </div>
            <div class="price q-mx-md"><span>{{cartItem.price.final}} تومان</span></div>
          </div>
        </div>
        <div class="product-detail row items-center justify-between ">
          <router-link :to="{name: 'User.Product.Show', params:{id: id}}" class="go-product text-primary text-center">
            رفتن
            به صفحه محصول
          </router-link>
          <q-expansion-item
            class="text-center"
            v-model="expanded"
            label="جزئیات محصول"
            style="max-width: 155px"
          >
            <q-card>
              <q-card-section>
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quidem, eius reprehenderit eos
                corrupti
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import {CartItem} from 'src/models/CartItem'
  import CartItemsGrandMode from 'components/Widgets/CheckoutReview/SideComponents/CartItemsGrandMode'

  export default {
    name: 'CartItem',
    components: {CartItemsGrandMode},
    props: {
      items: {
        type: Object,
        default() {
          return {};
        }
      },
      rawItem: {
        type: Object,
        default() {
          return {}
        }
      },
      hasGrand: {
        type: Boolean,
        default: false
      },
      id: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        cartItem: new CartItem(),
        loading: false,
        expanded: false,
        discount: 0,
        totalPrice: 0,
        infoDetails: [
          {
            name: 'main',
            icon: 'isax:teacher',
            title: 'گروه آموزشی آلاء',
            desc: ''
          },
          {
            name: 'major',
            icon: 'isax:book',
            title: 'رشته',
            desc: ''
          },
          {
            name: 'shipping_method',
            icon: 'isax:document-download',
            title: 'نحوه دریافت',
            desc: ''
          },
          {
            name: 'services',
            icon: 'isax:box',
            title: 'خدمات اصلی',
            desc: ''
          },
          {
            name: 'duration',
            icon: 'isax:clock',
            title: 'مدت برنامه',
            desc: ''
          },
          {
            name: 'educational_system',
            icon: 'isax:document-text',
            title: 'کنکور',
            desc: ''
          },
          {
            name: 'production_year',
            icon: 'isax:record',
            title: 'سال تولید',
            desc: ''
          }
          // {
          //   name: '',
          //   icon: 'isax:tag-user',
          //   title: 'دبیر',
          //   desc: ''
          // }
        ]
      }
    },
    watch: {
      rawItem: {
        handler(newValue, oldValue) {
          this.updateCartItem()
        },
        deep: true
      }
    },
    created() {
      this.updateCartItem()
      this.fillInfoDetailsGrand()
      this.fillInfoDetails()
    },
    mounted() {
      this.updateCartItem()
      this.fillInfoDetailsGrand()
      this.fillInfoDetails()
      this.calcTotalPrice()
      this.calcDiscount()
      // console.log(this.cartItem)
    },
    methods: {
      fillInfoDetails() {
        if (!this.cartItem.product.attributes) {
          return
        }
        const allInfos = this.cartItem.product.attributes.info
        this.infoDetails.forEach((info, index) => {
          if (allInfos[info.name]) {
            info.desc = this.getDescriptionString(allInfos[info.name])
          } else if (info.name !== 'main') {
            info.name = ''
          }
        })
      },
      fillInfoDetailsGrand() {
        if (this.hasGrand) {
          if (!this.cartItem.order_product[0].product.attributes) {
            return
          }
          const allInfos = this.cartItem.order_product[0].product.attributes.info
          this.infoDetails.forEach((info, index) => {
            if (allInfos[info.name]) {
              info.desc = this.getDescriptionString(allInfos[info.name])
            } else if (info.name !== 'main') {
              info.name = ''
            }
          })
        }
      },
      getDescriptionString(descArray) {
        let fullString = ''
        descArray.forEach((string, index) => {
          if (!descArray[index + 1]) {
            fullString += string
            return
          }
          fullString += string + ' . '
        })
        return fullString
      },
      deleteItem() {
      },
      toggleMenu() {
      },
      calcDiscount() {
        if (this.hasGrand) {
          let discount = 0
          this.cartItem.order_product.forEach(e => {
            discount += e.price.discount
          })
          this.discount = (discount / this.totalPrice) * 100
        } else {
          this.discount = (this.cartItem.price.discount / this.cartItem.price.base) * 100
        }
      },
      calcTotalPrice() {
        if (this.hasGrand) {
          this.cartItem.order_product.forEach(e => {
            this.totalPrice += e.price.final
          })
          this.cartItem.price.final = this.totalPrice
        }
      },
      updateCartItem() {
        if (this.hasGrand) {
          this.cartItem.product = this.rawItem.grand
          this.cartItem.order_product = this.rawItem.order_product
          return
        }
        this.cartItem = new CartItem(this.rawItem)
      }
    }
  }
</script>

<style lang="scss">
  .cart-item {
    .item-info-box {
      .info-photo {
        .q-img {
          border-radius: 10px;
        }
      }
    }
  }
</style>
<style lang="scss" scoped>
  * {
    font-size: 12px;
  }

  .cart-item {
    padding: 20px 20px;

    .title-above {
      font-weight: 500;
      font-size: 16px;
      line-height: 27px;
      margin-bottom: 10px;
    }

    .item-info-box {
      /*.info-photo {*/
      /*    width: 140px;*/
      /*    height: 140px;*/
      /*}*/

      .info-details {
        .title {
          font-weight: 500;
          font-size: 16px;
          line-height: 27px;
          margin-bottom: 10px;
        }

        .more-info-box {
          font-weight: 400;
          font-size: 12px;
          line-height: 20px;

          .info {
            margin-bottom: 8px;

            .info-desc {
              margin-left: 5px;
            }
          }
        }
      }

      .info-btn-box {
        text-align: right;
      }
    }

    .item-detail-box {
      .time-receive {
        width: 170px;
        background: linear-gradient(to right, rgba(255, 255, 255, 0) 0%, rgba(255, 144, 0, 0.2) 100%);
        border-radius: 6px;
        margin-top: 5px;
      }


      .price-detail {
        align-self: center;

        .price {
          font-weight: 500;
          font-size: 18px;
          color: #575962;
          margin-bottom: 2px;
        }
      }

      .discount-percent {
        border-radius: 8px 8px 8px 0px;
        background: #EF5350;
      }

      .price-base {
        text-decoration: line-through;
        color: #EF5350;
      }
    }
  }

  .q-item__section--main {
    .q-item__section--side {
      padding: 0 !important;
    }
  }

  .q-item {
    padding: 0 !important;
  }

  @media (max-width: 1439px) {
    .clear-space {
      display: none;
    }
  }

  @media (min-width: 600px) {
    .title-above {
      display: none;
    }
  }

  @media (max-width: 600px) {
    .title {
      display: none;
    }
    .cart-item {
      padding: 16px 16px;
    }
    .time-receive {
      margin-bottom: 20px;
    }
    .product-detail, .price-detail {
      width: 100%;
    }
  }
</style>
