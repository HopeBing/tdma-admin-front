<template>
  <a-layout :class="['admin-layout', fixedSideBar ? 'fixed-side-bar' : '']">
    <drawer v-if="isMobile" :openDrawer="collapsed" @change="onDrawerChange">
      <side-menu :theme="theme" :menuData="menuData" :collapsed="false" :collapsible="false" @menuSelect="onMenuSelect"/>
    </drawer>
    <side-menu :theme="theme" v-else-if="layout === 'side'" :menuData="menuData" :collapsed="collapsed" :collapsible="true" />
    <a-layout class="admin-layout-main beauty-scroll">
      <admin-header :style="headerStyle" :menuData="menuData" :collapsed="collapsed" @toggleCollapse="toggleCollapse"/>
      <a-layout-header v-if="fixedHeader"></a-layout-header>
      <a-layout-content class="admin-layout-content">
        <div :style="`min-height: ${minHeight}px; position: relative`">
          <slot></slot>
        </div>
      </a-layout-content>
      <a-layout-footer style="padding: 0px">
        <page-footer :link-list="footerLinks" :copyright="copyright" />
      </a-layout-footer>
    </a-layout>
  </a-layout>
</template>

<script>
import AdminHeader from './header/AdminHeader'
import PageFooter from './footer/PageFooter'
import Drawer from '../components/tool/Drawer'
import SideMenu from '../components/menu/SideMenu'
import {mapState} from 'vuex'

const minHeight = window.innerHeight - 64 - 24 - 122

let menuData = []

export default {
  name: 'AdminLayout',
  components: {SideMenu, Drawer, PageFooter, AdminHeader},
  data () {
    return {
      minHeight: minHeight,
      collapsed: false,
      menuData: menuData,
      showSetting: false
    }
  },
  provide() {
    return{
      layoutMinHeight: minHeight,
      menuI18n: require('@/router/i18n').default
    }
  },
  computed: {
    ...mapState('setting', ['isMobile', 'theme', 'layout', 'footerLinks', 'copyright', 'fixedHeader', 'fixedSideBar']),
    sideMenuWidth() {
      return this.collapsed ? '80px' : '256px'
    },
    headerStyle() {
      let width = (this.fixedHeader && this.layout == 'side' && !this.isMobile) ? `calc(100% - ${this.sideMenuWidth})` : '100%'
      let position = this.fixedHeader ? 'fixed' : 'static'
      let transition = this.fixedHeader ? 'transition: width 0.2s' : ''
      return `width: ${width}; position: ${position}; ${transition}`
    }
  },
  methods: {
    toggleCollapse () {
      this.collapsed = !this.collapsed
    },
    onDrawerChange (show) {
      this.collapsed = show
    },
    onMenuSelect () {
      this.toggleCollapse()
    },
    onSettingDrawerChange (val) {
      this.showSetting = val
    },
  },
  beforeCreate () {
    menuData = this.$router.options.routes.find((item) => item.path === '/').children
  }
}
</script>

<style lang="less" scoped>
  .admin-layout{
    &.fixed-side-bar{
      height: 100vh;
      .admin-layout-main{
        overflow: scroll;
      }
    }
    .admin-layout-main{
      .admin-header{
        top: 0;
        right: 0;
      }
    }
    .admin-layout-content{
      padding: 24px 24px 0;
      min-height: auto;
    }
    .setting{
      background-color: #1890ff;
      color: #fff;
      border-radius: 5px 0 0 5px;
      line-height: 40px;
      font-size: 22px;
      width: 40px;
      height: 40px;
      box-shadow: -2px 0 8px rgba(0, 0, 0, 0.15);
    }
  }
</style>
