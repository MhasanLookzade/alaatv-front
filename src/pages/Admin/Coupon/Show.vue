<template>
  <entity-show
    v-model:value="inputs"
    title="اطلاعات بن"
    :api="api"
    :entity-id-key="entityIdKey"
    :entity-param-key="entityParamKey"
    :edit-route-name="editRouteName"
    :index-route-name="indexRouteName"
  >
  </entity-show>
</template>

<script>
import { EntityShow } from 'quasar-crud'
import API_ADDRESS from 'src/api/Addresses'

export default {
  name: 'Show',
  components: { EntityShow },
  data () {
    return {
      inputs: [],
      api: API_ADDRESS.coupon.show.base,
      entityIdKey: '',
      entityParamKey: '',
      editRouteName: 'Admin.Coupon.Edit',
      indexRouteName: 'Admin.Coupon.Index'
    }
  }
}
</script>

<style scoped>

</style>
