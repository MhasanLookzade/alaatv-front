<template>
  <div></div>
</template>
<script>
export default {
  name: 'login',
  props: {
    finalPrice: {
      type: String,
      default: '0'
    }
  },
  data: () => ({
    loginInfo: {
      userName: {
        value: '',
        focus: false
      },
      password: {
        value: '',
        focus: false
      }
    },
    rules: {
      required: value => !!value || 'این فیلد الزامی است',
      password: value => (!!value && value.length === 10) || 'کد ملی می بایست ده رفم باشد'
    },
    errorMessage: ''
  }),
  methods: {
    loginClicked () {
      this.errorMessage = ''
      this.$refs.pass.blur()
      console.log('loginClicked')
      if (!this.loginInfo.userName.value && !this.loginInfo.password.value) {
        this.errorMessage = 'لطفا اطلاعات خود را وارد کنید'
        return
      } else if (!this.loginInfo.userName.value) {
        this.errorMessage = 'وارد کردن شماره همراه الزامی است'
        return
      } else if (!this.loginInfo.password.value) {
        this.errorMessage = 'وارد کردن کد ملی الزامی است'
        return
      }
      this.$emit('login', this.loginInfo)
    },
    getEnter (e) {
      this.errorMessage = ''
      console.log('e in login :', e)
      const actions = {
        pass: () => this.loginClicked(),
        userName: () => this.$refs.pass.focus()
      }
      if (e.keyCode === 13) actions[e.target.name].call()
    }
  }
}
</script>
<style scoped>
.v-text-field--outlined >>> fieldset {
    border-color: transparent;
}
</style>

<style lang="scss">

</style>
<style lang="scss" scoped>
P {
    margin-bottom: 0;
    color: #575962 !important;
}

.login-container {
    .form-box {
        padding: 34px 30px 0 30px;

        .alert-box {
            background-color: #9CEDAF;
            border-radius: 8px;
            padding: 0 16px;
            margin-bottom: 54px;

            .text-box {
                width: 100%;
                background-color: #F4F5F6;

                .title-style {
                    padding: 24px 0 12px 0;
                    font-weight: 500;
                    font-size: 14px;
                    line-height: 22px;
                }

                .description {
                    font-size: 12px;
                    padding: 0 24px 24px 24px;
                }
            }
        }

        .input-container {
            margin-bottom: 20px;

            .input-name {
                min-width: 76px;
                margin-left: 60px;
                font-weight: 500;
                font-size: 16px;
                line-height: 27px;
            }

            .input-box {
                width: 100%;
                border-radius: 8px;
                box-sizing: border-box !important;
                height: 40px;
                position: relative;

                .input-style {
                    font-size: 12px;
                    position: absolute;
                    width: 100%;
                    top: 0;
                }

                .input-focus {
                    top: -3px
                }
            }

            .input-blur {
                border: 1px solid #DDDDDD;
            }

            .input-box-focus {
                border: 3px solid #FFD9A8;
            }

        }
    }

    .sub-box {
        padding: 20px 16px 16px 16px;

        .login-btn-style {
            font-weight: 500;
            font-size: 16px;
            line-height: 27px;
            text-align: center;
            color: #FFFFFF;
            border-radius: 10px;
            box-shadow: none;
            letter-spacing: 0;
        }

        .err-text {
            padding-top: 10px;
            font-weight: 400;
            color: #EF5350 !important;
        }
    }

    .login-btn-mobile {
        display: none;
    }
}

@media only screen and (max-width: 1919px) {
    .login-container {
        padding: 28px 16px 16px 16px;

        .form-box {
            padding: 0;
        }

        .sub-box {
            padding: 20px 0 0 0;
        }
    }
}

@media only screen and (max-width: 1199px) {
    .login-container {
        padding: 4px 40px 30px 40px;

        .form-box {
            .alert-box {
                margin-bottom: 54px;
            }

            .inputs-container > .first {
                margin-left: 115px
            }

            .inputs-container {
                display: flex;

                .input-container {
                    margin-bottom: 20px;
                    width: calc(50% - 42.5px);

                    .input-name {
                        min-width: 76px;
                        margin-left: 40px;
                    }

                    .nation-code {
                        min-width: 46px;
                    }
                }

                .nation-code-box {
                    width: calc(50% - 72.5px);
                }
            }

        }
    }
}

@media only screen and (max-width: 1023px) {
    .login-container {
        padding: 26px 20px 20px 20px;

        .form-box {
            .alert-box {
                margin-bottom: 54px;
            }

            .inputs-container > .first {
                margin-left: 0
            }

            .inputs-container {
                flex-direction: column;

                .input-container {
                    margin-bottom: 20px;
                    width: 100%;

                    .input-name {
                        min-width: 76px;
                        margin-left: 252px;
                    }

                    .nation-code {
                        min-width: 76px;;
                    }
                }

                .nation-code-box {
                    width: 100%;
                }
            }
        }
    }

}

@media only screen and (max-width: 767px) {
    .login-container {
        padding: 22px 20px 20px 20px;

        .form-box {
            .alert-box {
                margin-bottom: 42px;
            }

            .inputs-container > .first {
                margin-left: 0
            }

            .inputs-container {
                flex-direction: column;

                .input-container {
                    margin-bottom: 20px;
                    width: 100%;

                    .input-name {
                        min-width: 76px;
                        margin-left: 120px;
                    }
                }
            }
        }
    }
}

@media only screen and (max-width: 575px) {
    .login-container {
        padding: 30px 16px 16px 16px;

        .form-box {
            .alert-box {
                margin-bottom: 42px;
            }

            .inputs-container {
                display: none;
            }
        }

        .login-btn-mobile {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 64px;
            padding: 0 20px;
            background-color: #FFFFFF;
            box-shadow: 0px -2px 5px rgba(64, 123, 177, 0.1);
            display: flex;
            align-items: center;
            justify-content: space-between;
            z-index: 100000000000000;
            border-radius: 20px 20px 0 0 !important;

            .price-box {
                font-weight: 500;
                color: #575962;
                letter-spacing: -0.03em;

                .title-des {
                    font-size: 10px;
                    line-height: 17px;
                    margin-left: 13px;
                }

                .final-price {
                    font-size: 14px;
                    line-height: 22px;
                }

                .currency {
                    font-size: 10px;
                    line-height: 16px;
                    margin-right: 2px;
                }
            }

            .sub-btn-style {
                width: 118px;
                border-radius: 8px;
                letter-spacing: 0;
                font-size: 16px;
            }
        }
    }
}
</style>
