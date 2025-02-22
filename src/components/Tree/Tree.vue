<template>
  <q-tree
    class="q-ma-lg"
    :nodes="nodes"
    no-nodes-label="درختی ایجاد نشده است!"
    node-key="id"
    ref="tree"
    control-color="secondary"
    label-key="title"
    icon="isax:add-square"
    :tick-strategy="tickStrategy"
    v-model:ticked="ticked"
    @update:ticked="tickedNode"
    @lazy-load="getChildOfNode"
  >
    <template v-slot:default-header="prop">
      <span class="node-title">
        {{ prop.node.title }}
        <q-icon
          :class="editable ? 'edit-btn': 'none-edit-btn'"
          name="edit"
          @click.stop
          @keypress.stop
          @click="openEditMenu(prop.node) "
        />
      </span>
    </template>
  </q-tree>
    <q-btn
      v-if="editable && nodes && !nodes.length"
      label="ساخت درخت"
      @click="toggleMenu(true)"
      icon="add"
      color="green"
      flat
    />
  <q-dialog v-model="editDialog " persistent>
    <q-card class="q-pa-md ">
      <q-btn flat icon="close " color="red " v-close-popup @click="toggleMenu(false)" />
      <q-tabs
        v-model="tab "
        narrow-indicator
        dense
      >
        <q-tab class="text-purple" name="editNode" icon="edit" label="ویرایش "/>
        <q-tab class="text-orange" name="createNewNode" icon="add" label="اضافه کردن گره جدید "/>
      </q-tabs>
      <q-tab-panels v-model="tab " animated>
        <q-tab-panel name="editNode">
          <q-input
            class="q-ma-md"
            filled
            v-model="editedTitle "
            label="نام جدید "
          />
          <q-input
            class="q-ma-md"
            filled
            v-model="editedOrder"
            label="ترتیب جدید "
          />
          <q-btn
            color="green "
            :loading="loading "
            @click="edit"
          >
            ثبت
          </q-btn>
        </q-tab-panel>
        <q-tab-panel name="createNewNode">
          <q-input
            class="q-ma-md"
            filled
            v-model="newTitle"
            label="نام "
          />
          <q-input
            class="q-ma-md"
            filled
            v-model="newOrder"
            label="ترتیب "
          />
          <q-btn
            color="green "
            :loading="loading "
            @click="addNode() "
          >
            اضافه شود
          </q-btn>
        </q-tab-panel>
      </q-tab-panels>
    </q-card>
  </q-dialog>
</template>
<script>
import { TreeNode, TreeNodeList } from 'src/models/TreeNode'
// "\e90b"
// "\eb21"
// 'iconsax' !important
export default {
  name: 'Tree',
  props: {
    tickStrategy: {
      default: 'none'
    },
    editable: {
      type: Boolean,
      default: false
    },
    getNodeById: {
      type: Function,
      default: (id, done, fail, callback) => {
      }
    },
    addNewNode: {
      type: Function,
      default: (ParentId, title, order, callback) => {}
    },
    editNode: {
      type: Function,
      default: (id, title, order, callback) => {}
    }
  },
  data: () => {
    return {
      ticked: [],
      completeTickedNode: [],
      nodes: [],
      tab: 'createNewNode',
      loading: false,
      newTitle: '',
      newOrder: 1,
      editedTitle: '',
      editedOrder: 1,
      selectedNode: {},
      editDialog: false
    }
  },
  created () {},
  mounted () {},
  emits: ['ticked', 'lazy-loaded'],
  methods: {
    createRoot (nodeData) {
      const treeNodeData = new TreeNode(nodeData)
      treeNodeData.children = (new TreeNodeList(treeNodeData.children)).list
      this.nodes = [treeNodeData]
    },

    tickedNode (target) {
      this.completeTickedNode = []
      target.forEach(id => {
        this.completeTickedNode.push(this.nodes[0].findNode(id))
      })
      this.$emit('ticked', this.completeTickedNode)
    },

    loadChildOfNode (node, done) {
      const tree = []
      node.children.forEach(child => {
        tree.push(new TreeNode({ id: child.id, title: child.title, parent: node.id }))
      })
      done(tree)
      this.$emit('lazy-loaded', tree)
    },

    getChildOfNode ({ node, key, done, fail }) {
      if (node.children && node.children.length) {
        this.showChildOfNodeFromCache(node, key, done, fail)
      } else {
        this.showChildOfNodeFromServer(node, key, done, fail)
      }
    },

    showChildOfNodeFromCache (node, key, done, fail) {
      const tree = []
      node.children.forEach(child => {
        tree.push(new TreeNode({ id: child.id, title: child.title, order: child.order, parent: node.id }))
      })
      done(tree)
      this.$emit('lazy-loaded', tree)
    },

    showChildOfNodeFromServer (node, key, done, fail) {
      this.getNodeById(node.id, done, fail, this.loadChildOfNode)
    },

    edit () {
      this.editNode(this.selectedNode.id, this.editedTitle, this.editedOrder)
        .then(() => {
          const id = this.selectedNode.id
          const node = this.$refs.tree.getNodeByKey(id)
          node.title = this.editedTitle
          node.order = this.editedOrder
          this.editDialog = false
        }).catch(err => {
          console.log(err)
        })
    },

    addNode () {
      const id = this.selectedNode.id
      const getNode = this.$refs.tree.getNodeByKey(id)
      this.addNewNode(id, this.newTitle, this.newOrder)
        .then(response => {
          getNode.children.unshift(new TreeNode({
            id: response.data.data.id,
            type: response.data.data.type,
            title: response.data.data.title,
            parent: response.data.data.parent.id
          }))
          this.editDialog = false
        })
    },

    openEditMenu (node) {
      this.selectedNode = {
        id: node.id,
        title: node.title,
        order: node.order
      }
      this.editedTitle = this.selectedNode.title
      this.editedOrder = this.selectedNode.order
      this.editDialog = true
    },

    setNodesTicked (keys, state) {
      if (!state) {
        state = false
      }
      this.$refs.tree.setTicked(keys, state)
    },

    toggleMenu (state) {
      this.editDialog = state
    }
  }
}
</script>
<style scoped lang='scss'>
  .q-tree {
    display: inline-block;

    .node-title {
      color: var(--alaa-TextPrimary);
      &:hover {
        .edit-btn {
          color: #f18305;
        }
      }
      .edit-btn {
        color: transparent;
      }
      .none-edit-btn{
        display: none;
      }
    }
  }
</style>
<style lang='scss'>
.q-tree {
  .q-tree__node--parent {
    .q-tree__node-header {
      .q-icon {
        font-size: 20px;
        margin-right: 8px;

        &.q-tree__arrow, &.q-tree__arrow--rotate {
          transform: none !important;
          transition: none !important;
        }

        &.q-tree__arrow--rotate {
          &:before {
            content: "\eb21";
          }
        }
      }
    }
  }
}

  .q-tree__node:after {
    right: auto;
    left: -13px;
    top: 0;
    bottom: -31px;
    border-left: 2px solid #d5d6da;
  }

  .q-tree__node-header::before {
    border-bottom: 0 !important;
    left: -35px;
    bottom: 2px;
    border-left: 2px solid #d5d6da;
  }

</style>
