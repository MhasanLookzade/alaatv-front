<template>
  <!--  v-model:index-inputs="indexInputs"-->
  <entity-crud
    v-model:default-inputs="defaultInputs"
    v-model:index-inputs="indexInputs"
    v-bind="allProps"
  >
    <template v-slot:entity-crud-table-cell="{inputData, showConfirmRemoveDialog}">
      <q-td :props="inputData.props">
        <template v-if="inputData.props.col.name === 'actions'">
          <q-btn round flat dense size="md" color="info" icon="info" :to="{name:'Admin.SourceManagement.Edit', params: {id: inputData.props.row.id}}">
            <q-tooltip>
              ویرایش
            </q-tooltip>
          </q-btn>
          <q-btn round flat dense size="md" color="negative" icon="delete" class="q-ml-md"
                 @click="showConfirmRemoveDialog(inputData.props.row, 'id', getRemoveMessage(inputData.props.row))">
            <q-tooltip>
              حذف
            </q-tooltip>
          </q-btn>
        </template>
        <template v-else-if="inputData.props.col.name === 'description'">
          <div v-html="inputData.props.value" />
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
  name: 'SourceManagement',
  components: {
    EntityCrud
  },
  data () {
    return {
      model: null,
      tags: [],
      expanded: true,
      allProps: {
        config: {
          api: {
            show: API_ADDRESS.sourceManagement.show.base,
            edit: API_ADDRESS.sourceManagement.edit.base,
            create: API_ADDRESS.sourceManagement.create.base,
            index: API_ADDRESS.sourceManagement.index.base
          },
          title: {
            show: 'اطلاعات  منبع ها',
            edit: 'ویرایش  منبع ها',
            create: 'ایجاد منبع جدید',
            index: 'لیست  منبع ها'
          },
          showRouteName: 'Admin.SourceManagement.Show',
          editRouteName: 'Admin.SourceManagement.Edit',
          indexRouteName: 'Admin.SourceManagement.Index',
          createRouteName: 'Admin.SourceManagement.Create',
          tableKeys: {
            data: 'data',
            total: 'meta.total',
            currentPage: 'meta.current_page',
            perPage: 'meta.per_page',
            pageKey: 'productPage'
          },
          table: {
            columns: [
              {
                name: 'mobile',
                required: true,
                label: 'نام',
                align: 'left',
                field: row => row.id
              },
              {
                name: 'national_code',
                required: true,
                label: 'لینک',
                align: 'left',
                field: row => row.id
              },
              {
                name: 'phone_number',
                required: true,
                label: 'عکس',
                align: 'left',
                field: row => row.mobile
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
        { type: 'input', name: 'name', value: null, label: 'کد', col: 'col-md-3' },
        { type: 'input', name: 'name', value: null, label: 'نام پکیج', col: 'col-md-3' },
        { type: 'file', name: 'photo', label: 'عکس منابع', responseKey: 'data.photo', size: '250px', col: 'col-md-3' }
      ],
      createInputs: [],
      editInputs: [],
      showInputs: [],
      indexInputs: [
        { type: 'input', name: 'name', value: null, label: 'نام', col: 'col-md-3' }
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
    createValue (val, done) {
      // Calling done(var) when new-value-mode is not set or "add", or done(var, "add") adds "var" content to the model
      // and it resets the input textbox to empty string
      // ----
      // Calling done(var) when new-value-mode is "add-unique", or done(var, "add-unique") adds "var" content to the model
      // only if is not already set
      // and it resets the input textbox to empty string
      // ----
      // Calling done(var) when new-value-mode is "toggle", or done(var, "toggle") toggles the model with "var" content
      // (adds to model if not already in the model, removes from model if already has it)
      // and it resets the input textbox to empty string
      // ----
      // If "var" content is undefined/null, then it doesn't tampers with the model
      // and only resets the input textbox to empty string

      if (val.length > 0) {
        if (!this.tags.includes(val)) {
          this.tags.push(val)
        }
        done(val, 'toggle')
      }
    }
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
