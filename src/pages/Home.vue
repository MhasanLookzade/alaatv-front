<template>
  <div class="home-page">
    <page-builder
      :sections="sections"
      :options="pageConfig"
    />
  </div>
</template>

<script>
import PageBuilder from 'components/PageBuilder/PageBuilder'
import API_ADDRESS from 'src/api/Addresses'
import GetWidgetsData from 'assets/js/GetWidgetsData'

export default {
  name: 'BaseComponent',
  components: { PageBuilder },
  data() {
    return {
      pageConfig: {
        padding: {
          a: 'md'
        }
      },
      sections: [
        {
          data: {
            rows: [
              {
                cols: [
                  {
                    widgets: [
                      {
                        name: 'blockList',
                        data: API_ADDRESS.pages.home,
                        options: {
                          to: 1
                        }
                      },
                      {
                        name: 'services',
                        data: [
                          {
                            link: '#konkoor2',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_konkur_icon.png?w=52&h=52',
                            title: 'کنکور',
                            subTitle: 'صفر تا صد رایگان'
                          },
                          {
                            link: 'https://3a.alaatv.com/',
                            icon: 'https://node6.alaatv.com/upload/homepage_icon_3a_icon.png?w=52&h=52',
                            title: 'سه آ',
                            subTitle: 'آزمون آنلاین آلاء'
                          },
                          {
                            link: '#yazdahom',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_grade11_icon.png?w=52&h=52',
                            title: 'یازدهم',
                            subTitle: 'صفر تا صد رایگان'
                          },
                          {
                            link: '#dahom',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_grade10_icon.png?w=52&h=52',
                            title: 'دهم',
                            subTitle: 'صفر تا صد رایگان'
                          },
                          {
                            link: 'asset/abrisham',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_silkroad2_icon.png?w=52&h=52',
                            title: 'همایش راه ابریشم',
                            subTitle: 'برنامه ای کامل ویژه کنکوری ها'
                          },
                          {
                            link: '/landing/10',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_godar_icon.png?w=52&h=52',
                            title: 'همایش گدار',
                            subTitle: 'جمع بندی نیم سال اول دوازدهم'
                          },
                          {
                            link: '/landing/9',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_taftan_icon.png?w=52&h=52',
                            title: 'همایش تفتان',
                            subTitle: 'جمع بندی دهم و یازدهم'
                          },
                          {
                            link: '/landing/15',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_arash_icon.png?w=52&h=52',
                            title: 'همایش آرش',
                            subTitle: 'جمع بندی کامل کنکور'
                          },
                          {
                            link: '/h',
                            icon: 'https://nodes.alaatv.com/upload/homepage_icon_hekmat_icon.png?w=52&h=52',
                            title: 'طرح حکمت',
                            subTitle: 'ویژه خانواده های نیروهای مسلح'
                          }
                        ],
                        options: {
                          style: {
                            'margin-bottom': '20px'
                          }
                        }
                      },
                      {
                        name: 'blockList',
                        data: API_ADDRESS.pages.home,
                        options: {
                          from: 1
                        }
                      }
                    ]
                  }
                ],
                options: {
                  boxed: true,
                  boxedWidth: 1362
                }
              }
            ]
          },
          options: {
            fullHeight: true,
            verticalAlign: 'center'
          }
        }
      ]
    }
  },
  methods: {}
}
</script>

<style
  lang="scss"
  scoped
>
.home-page {
  &:deep(.banner-header-0) {
    display: none;
  }
}
</style>
