<template>
  <entity-crud
    v-model:index-inputs="indexInputs"
    v-model:default-inputs="defaultInputs"
    v-bind="allProps"
  >
    <template v-slot:entity-crud-table-cell="{inputData, showConfirmRemoveDialog}">
      <q-td :props="inputData.props">
        <template v-if="inputData.props.col.name === 'photo'">
          <q-avatar>
            <q-img
              :src="inputData.props.value"
              placeholder-src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAJYAAACWBAMAAADOL2zRAAAAG1BMVEXMzMyWlpaqqqq3t7fFxcW+vr6xsbGjo6OcnJyLKnDGAAAACXBIWXMAAA7EAAAOxAGVKw4bAAABAElEQVRoge3SMW+DMBiE4YsxJqMJtHOTITPeOsLQnaodGImEUMZEkZhRUqn92f0MaTubtfeMh/QGHANEREREREREREREtIJJ0xbH299kp8l8FaGtLdTQ19HjofxZlJ0m1+eBKZcikd9PWtXC5DoDotRO04B9YOvFIXmXLy2jEbiqE6Df7DTleA5socLqvEFVxtJyrpZFWz/pHM2CVte0lS8g2eDe6prOyqPglhzROL+Xye4tmT4WvRcQ2/m81p+/rdguOi8Hc5L/8Qk4vhZzy08DduGt9eVQyP2qoTM1zi0/uf4hvBWf5c77e69Gf798y08L7j0RERERERERERH9P99ZpSVRivB/rgAAAABJRU5ErkJggg=="
              spinner-color="white"
              style="height: 50px; max-width: 50px"
            />
          </q-avatar>
        </template>
        <template v-else-if="inputData.props.col.name === 'actions'">
          <q-btn round flat dense size="md" color="info" icon="info" :to="{name:'Admin.Roles.Edit', params: {id: inputData.props.row.id}}">
            <q-tooltip>
              اصلاح
            </q-tooltip>
          </q-btn>
          <q-btn round flat dense size="md" color="negative" icon="delete" class="q-ml-md"
                 @click="showConfirmRemoveDialog(inputData.props.row, 'id', getRemoveMessage(inputData.props.row))">
            <q-tooltip>
              حذف
            </q-tooltip>
          </q-btn>
        </template>
        <template v-else>
          {{ inputData.props.value }}
        </template>
      </q-td>
    </template>
  </entity-crud>
</template>

<script>
import API_ADDRESS from 'src/api/Addresses'
import EntityCrud from 'components/EntityCrud'

export default {
  name: 'Roles',
  components: {
    EntityCrud
  },
  data () {
    return {
      allProps: {
        config: {
          api: API_ADDRESS.permission.show.base,
          // or
          // api: {
          //   show: API_ADDRESS.users.show.base,
          //   edit: API_ADDRESS.users.edit.base,
          //   create: API_ADDRESS.users.create.base,
          //   index: API_ADDRESS.users.index.base
          // },
          title: {
            show: 'اطلاعات نقش',
            edit: 'اطلاعات نقش',
            create: 'افزودن نقش',
            index: 'لیست نقش ها'
          },
          // or
          // title: 'اطلاعات کاربر',
          showRouteName: 'Admin.Roles.Show',
          editRouteName: 'Admin.Roles.Edit',
          indexRouteName: 'Admin.Roles.Index',
          createRouteName: 'Admin.Roles.Create',
          tableKeys: {
            data: 'data',
            total: 'total',
            currentPage: 'page',
            perPage: 'per_page',
            pageKey: 'page'
          },
          table: {
            columns: [
              {
                name: 'id',
                required: true,
                label: 'id',
                align: 'left',
                field: row => row.id
              },
              {
                name: 'last_name',
                required: true,
                label: 'نام اصلی',
                align: 'left',
                field: row => row.last_name
              },
              {
                name: 'first_name',
                required: true,
                label: 'نام نقش',
                align: 'left',
                field: row => row.first_name
              },
              {
                name: 'national_code',
                required: true,
                label: 'تاریخ درج',
                align: 'left',
                field: row => row.national_code
              },
              {
                name: 'national_code',
                required: true,
                label: 'تاریخ اصلاح',
                align: 'left',
                field: row => row.national_code
              },
              {
                name: 'email',
                required: true,
                label: 'توضیح',
                align: 'left',
                field: row => row.email
              },
              {
                name: 'email',
                required: true,
                label: 'دسترسی های این نقش ',
                align: 'left',
                field: row => row.email
              },
              {
                name: 'actions',
                required: true,
                label: 'عملیات',
                align: 'left',
                field: ''
              }
            ],
            data: []
          }
        }
      },
      defaultInputs: [
        { type: 'input', name: 'first_name', responseKey: 'data.first_name', value: 'null', label: 'نام ', col: 'col-md-3' },
        { type: 'input', name: 'first_name', responseKey: 'data.first_name', value: 'null', label: 'نام قابل نمایش ', col: 'col-md-3' },
        { type: 'space', col: 'col-md-12' },
        { type: 'select', name: 'content_type_id', label: 'دسترسی', col: 'col-md-3', multiple: true, value: null, options: [{ label: 'تاریخ ایجاد اولیه', value: 0 }, { label: 'توضیحات مشتری', value: 8 }, { label: 'عملیات', value: 3 }] }
      ],
      createInputs: [],
      editInputs: [],
      showInputs: [],
      indexInputs: [
        { type: 'input', name: 'id', value: null, label: 'نام اصلی', col: 'col-md-3' },
        { type: 'input', name: 'name', value: null, label: 'نام نقش', col: 'col-md-3' },
        { type: 'date', name: 'created_at_range', value: [], label: 'تاریخ درج', col: 'col-md-3' },
        { type: 'date', name: 'created_at_range', value: [], label: 'تاریخ اصلاح', col: 'col-md-3' },
        { type: 'input', name: 'name', value: null, label: 'دسترسی های این نقش', col: 'col-md-5' },
        { type: 'input', name: 'name', value: null, label: 'توضیح', col: 'col-md-5' }
      ]
    }
  },
  methods: {
    // for index.vue
    getRemoveMessage (row) {
      const firstName = row.first_name
      const lastName = row.last_name
      return 'آیا از حذف ' + firstName + ' ' + lastName + ' اطمینان دارید؟'
    },
    testMethod () {},
    testMethod1 () {},
    testMethod2 () {},
    testMethod3 () {}
  },
  watch: {
    // editInputs: {
    //   handler (newValue, oldValue) {
    //     console.log('inputs', newValue)
    //   },
    //   deep: true
    // }
  },
  created () {}
}
</script>

<style scoped>

</style>
