<template>
  <page-builder
    :sections="sections"
    :containerHeight="calculateHeightStyle"
    :options="pageConfig" />
</template>

<script>
import PageBuilder from 'components/PageBuilder/PageBuilder'
export default {
  name: 'MyFavorites',
  components: { PageBuilder },
  data () {
    return {
      pageConfig: {
        padding: {
          a: 'lg'
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
                        name: 'favorites'
                      }
                    ]
                  }
                ],
                options: {
                  boxed: false
                }
              }
            ]
          }
        }
      ]
    }
  },
  computed: {
    calculateHeightStyle () {
      return this.$store.getters['AppLayout/calculateContainerFullHeight']
    }
  }
}
</script>
