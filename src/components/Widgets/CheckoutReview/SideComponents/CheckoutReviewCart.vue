<template>
  <div>
    <div class="checkout-review-cart bg-white shadow-4 q-mx-sm q-mt-md q-px-lg">
      <div class=" q-pt-lg flex justify-between">
        <p>جمع سبد خرید({{number}})</p>
        <p>{{totalPrice}} تومان</p>
      </div>
      <div v-if="isLogedIn" class=" q-pt-lg flex justify-between">
        <p>اعتبار کیف پول</p>
        <p> تومان</p>
      </div>
      <div class=" text-red q-pt-lg flex justify-between">
        <p>سود شما از این خرید</p>
        <p>{{profit}} تومان</p>
      </div>
      <q-separator/>
      <div v-if="isLogedIn" class=" q-pt-lg row">
        <p class="col-4">کد تخفیف</p>
        <q-input class="col-8" outlined bottom-slots v-model="discount" label="افزودن کد تخفیف" :dense="true">

          <template v-slot:append>
            <q-btn color="black" round dense flat label="ثبت"/>
          </template>
        </q-input>
      </div>
      <q-separator v-if="isLogedIn"/>
      <div class="parent" v-if="isLogedIn">
        <div class="item-1 q-pt-lg flex justify-between">
          <p>مبلغ قابل پرداخت</p>
          <p>{{payable}} تومان</p>
        </div>
        <q-input class="item-2 full-height" clearable outlined v-model="explanation"
                 type="textarea"
                 label="اگر توضیحی درباره سفارش خود دارید اینجا بنویسید..."/>
        <div class="item-3 flex justify-between items-center">
          <p class="q-my-md ">درگاه پرداخت</p>
          <div class="flex justify-center">
            <q-option-group
              class="q-mt-sm"
              v-model="group"
              :options="options"
              color="primary"
              inline
              left-label>
              <template v-slot:label="opt">
                <div class="">
                  <div class="bg-grey-3" style="width: 58px;height: 58px">
                  </div>
                </div>
              </template>
            </q-option-group>
          </div>
        </div>
        <q-btn color="primary" class="q-my-md item-4 full-width" label="ادامه و ثبت سفارش"/>
      </div>
      <div v-else>
        <div class="login-text bg-green-3 q-px-md q-my-xl">
          <div class="bg-grey-3 q-pa-md text-center">
            <p>پیش از ثبت سفارش وارد حساب کاربری خود شوید</p>
            <p>اگر حساب کاربری در آلاء ندارید با وارد کردن شماره همراه و کد ملی خود میتوانید به سادگی حساب خود را ایجاد
              کنید</p>
          </div>
        </div>
        <div class="row justify-between">
          <div class="row justify-center col-sm-5 col-md-12 col-xs-12">
            <span class="col-4 q-mt-sm">شماره همراه</span>
            <q-input class="phone-number q-mb-md col-8" dir="ltr" dense clearable outlined v-model="phoneNumber"
                     placeholder="09........."/>
          </div>
          <div class="row justify-between col-sm-5 col-md-12 col-xs-12">
            <span class="col-4 q-mt-sm">کد ملی</span>
            <q-input class="natinalo-code q-mb-md col-8" dir="ltr" dense clearable outlined
                     v-model="nationalCode"
                     placeholder="..........."/>
          </div>
        </div>
        <q-btn color="green-6" @click="this.isLogedIn=true" class="q-my-md full-width" label="ورود/ثبت نام"/>
      </div>
    </div>
    <div v-if="isLogedIn" class="payment-summary bg-white items-center row q-my-md q-mx-sm q-pa-md"
         style="display: none">
      <span class="col">مبلغ قابل پرداخت:</span>
      <span class="col">{{payable}}</span>
      <q-btn class="col" color="primary" label="ثبت سفارش"/>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'CheckoutReviewCart',
    props: {
      items: {
        type: Object,
        default() {
          return {}
        }
      }
    },
    data() {
      return {
        explanation: "",
        phoneNumber: "",
        nationalCode: "",
        discount: "",
        options: [
          {
            label: '',
            value: 'op1'
          },
          {
            label: '',
            value: 'op2'
          }
        ],
        group: 'op1',
        isLogedIn: true,
        number: 0,
        totalPrice: 0,
        payable: 0,
        profit: 0
      }
    },
    mounted() {
      this.calcPricesAndNumber()
    },
    methods: {
      calcPricesAndNumber() {
        this.items.forEach(e => {
          if (e.grand) {
            this.number += 1
            e.order_product.forEach(el => {
              this.totalPrice += el.price.base
              this.payable += el.price.final
            })
          } else {
            this.number += e.order_product.length
            console.log(e)
            e.order_product.forEach(el => {
              this.totalPrice += el.price.base
              this.payable += el.price.final
            })
          }
        })
        this.profit = this.totalPrice - this.payable
      },
    }
  }

</script>

<style lang="scss" scoped>
  .q_radio {
    border: 1px solid grey;
    border-radius: 5px;
  }

  @media (max-width: 1024px) {
    .parent {
      display: grid;
      grid-template-rows: 80px 80px 80px;
      grid-template-columns: 1fr 1fr;

      .item-1 {
        grid-row: 1/2;
        grid-column: 1/2;
      }

      .item-2 {
        padding-top: 10px;
        margin-left: 10px;
        grid-row: 1/3;
        grid-column: 2/3;
      }

      .item-3 {
        grid-row: 2/3;
        grid-column: 1/2;
      }

      .item-4 {
        grid-row: 3/4;
        grid-column: 1/3;
      }
    }
  }

  @media (max-width: 600px) {
    .payment-summary {
      display: flex !important;
      border-radius: 20px 20px 0px 0px;
    }
    .parent {
      display: block;

      .item-4 {
        display: none;
      }
    }
  }

  .checkout-review-cart {
    border-radius: 10px;
  }

  .border {
    border: 1px solid #000000;
    border-radius: 8px;
  }

  .login-text {
    border-radius: 8px;
  }
</style>
