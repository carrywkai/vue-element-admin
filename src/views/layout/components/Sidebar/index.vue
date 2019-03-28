<template>
  <!-- 左侧导航栏的外层盒子 -->
  <el-scrollbar wrap-class="scrollbar-wrapper">
    <!-- 导航栏的内层盒子:内层盒子高度大于外层盒子-->
    <el-menu
      :default-active="$route.path"
      :collapse="isCollapse"
      :background-color="variables.menuBg"
      :text-color="variables.menuText"
      :active-text-color="variables.menuActiveText"
      :collapse-transition="false"
      mode="vertical"
    >
      <!-- 侧边导航元素 -->
      <sidebar-item v-for="route in routes" :key="route.path" :item="route" :base-path="route.path"/>

    </el-menu>
  </el-scrollbar>
</template>

<script>
import { mapGetters } from 'vuex'
import variables from '@/styles/variables.scss'
import SidebarItem from './SidebarItem'

export default {
  components: { SidebarItem },
  computed: {
    ...mapGetters([
      'sidebar'
    ]),
    routes() {
      // console.log(this.$router.options)
      // this.$router.options.routes 这是路由规则
      return this.$router.options.routes
    },
    variables() {
      return variables
    },
    isCollapse() {
      return !this.sidebar.opened
    }
  }
}
</script>
