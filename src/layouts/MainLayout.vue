<template>
  <div class="main-layout">
    <quasar-template-builder v-model:value="properties"
                             @onResize="resize">
      <template #header>
        <template-header />
        <q-linear-progress
          v-if="$store.getters['loading/loading']"
          color="primary"
          reverse
          class="q-mt-sm"
          indeterminate
        />
        <q-resize-observer @resize="onHeaderResize" />
      </template>
      <template #left-drawer>
        <side-menu-dashboard />
      </template>
      <template #content>
        <div ref="contentInside"
             class="content-inside"
             v-scroll="onContentInsideScroll"
        >
          <q-dialog v-model="confirmDialogData.show"
                    persistent>
            <q-card class="q-pa-md q-pb-none">
              <q-card-section>
                <q-icon name="warning"
                        color="warning"
                        size="2rem" />
                {{confirmDialogData.message}}
              </q-card-section>
              <q-separator />
              <q-card-actions align="right"
                              class="q-pb-none">
                <q-btn color="green"
                       flat
                       @click="confirmDialogAction(true)"
                       v-close-popup>بله</q-btn>
                <q-btn color="red"
                       flat
                       @click="confirmDialogAction(false)"
                       v-close-popup>خیر</q-btn>
              </q-card-actions>
            </q-card>
          </q-dialog>
          <Router :include="keepAliveComponents" />
        </div>
      </template>
    </quasar-template-builder>
  </div>
</template>

<script>
import SideMenuDashboard from 'components/Menu/SideMenu/SideMenu-dashboard'
import { QuasarTemplateBuilder } from 'quasar-template-builder'
import templateHeader from 'components/Template/templateHeader'
import Router from 'src/router/Router'
import KeepAliveComponents from 'assets/js/KeepAliveComponents'
import { setHeight } from 'src/boot/page-builder'

export default {
  components: { Router, SideMenuDashboard, QuasarTemplateBuilder, templateHeader },
  data () {
    return {
      contentVerticalScrollPosition: 0,
      keepAliveComponents: KeepAliveComponents,
      properties: {
        layoutView: 'hHh LpR fFf',
        layoutHeader: true,
        layoutHeaderVisible: true,
        layoutHeaderReveal: false,
        layoutHeaderElevated: false,
        layoutHeaderBordered: false,
        layoutLeftDrawer: true,
        layoutLeftDrawerVisible: false,
        layoutLeftDrawerOverlay: true,
        layoutLeftDrawerElevated: true,
        layoutLeftDrawerBordered: false,
        layoutLeftDrawerWidth: 325,
        layoutPageContainer: true,
        layoutRightDrawer: false,
        layoutFooter: false,
        layoutHeaderCustomClass: 'main-layout-header row',
        layoutLeftDrawerCustomClass: 'main-layout-left-drawer',
        layoutPageContainerCustomClass: 'main-layout-container'
      }
    }
  },
  computed: {
    confirmDialogData () {
      return this.$store.getters['AppLayout/confirmDialog']
    },
    calculateHeightStyle() {
      return this.$store.getters['AppLayout/calculateContainerFullHeight']
    }
  },
  methods: {
    onContentInsideScroll (data) {
      this.$store.commit('AppLayout/updateLayoutHeaderElevated', data > 0)
    },
    confirmDialogAction (data) {
      if (this.confirmDialogData) {
        this.confirmDialogData.callback(data)
      } else {
        this.$store.commit('AppLayout/showConfirmDialog', {
          show: false
        })
      }
    },
    onHeaderResize (value) {
      this.setHeaderDimension(value)
      this.$store.commit('AppLayout/updateHeaderSize', value)
    },
    setHeaderDimension (value) {
      this.$refs.contentInside.style.height = 'calc(100vh +' + value.height + 'px'
    },
    resize (val) {
      this.$store.commit('AppLayout/updateWindowSize', val)
      if (val.width > 1439) {
        this.$store.commit('AppLayout/updateLayoutLeftDrawerWidth', 314)
        this.$store.commit('AppLayout/updateLayoutLeftDrawerBehavior', 'desktop') && this.$store.commit('AppLayout/updateLayoutRightDrawerBehavior', 'desktop')
      } else if (val.width > 599) {
        this.$store.commit('AppLayout/updateLayoutLeftDrawerWidth', 280)
        this.$store.commit('AppLayout/updateLayoutLeftDrawerBehavior', 'mobile') && this.$store.commit('AppLayout/updateLayoutRightDrawerBehavior', 'mobile')
      } else {
        this.$store.commit('AppLayout/updateLayoutLeftDrawerWidth', 242)
        this.$store.commit('AppLayout/updateLayoutLeftDrawerBehavior', 'mobile') && this.$store.commit('AppLayout/updateLayoutRightDrawerBehavior', 'mobile')
      }
    }
  },
  created() {
    setHeight(this.calculateHeightStyle)
  }
}
</script>

<style lang="scss" scoped>
.main-layout {
  :deep(.q-header) {
    background-color: #FFFFFF;
    display: flex;
    flex-direction: row;
    padding: 16px 0;
  }
  :deep(.main-layout-container) {
    background-color: #f1f1f1;
  }
  :deep(.main-layout-left-drawer) {
    background-color: #f1f1f1;
  }
}
</style>
