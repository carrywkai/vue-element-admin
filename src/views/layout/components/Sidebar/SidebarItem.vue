<template>
  <div v-if="!item.hidden" class="menu-wrapper">
    <!-- 一级导航 -->
    <!-- & 和 && 都表示 与运算，当表达式二端的运算结果都为 true 时候，结果才为true -->
    <!-- && 还具有短路的功能，只要第一个表达式为false，后面就不需要运算了 -->
    <template v-if="hasOneShowingChild(item.children,item) && (!onlyOneChild.children||onlyOneChild.noShowingChildren) && !item.alwaysShow">
      <app-link :to="resolvePath(onlyOneChild.path)">
        <el-menu-item :index="resolvePath(onlyOneChild.path)" :class="{'submenu-title-noDropdown':!isNest}">
          <item :meta="Object.assign({},item.meta,onlyOneChild.meta)" />
        </el-menu-item>
      </app-link>
    </template>
    <!-- 二级导航 -->
    <el-submenu v-else ref="subMenu" :index="resolvePath(item.path)" popper-append-to-body>
      <template slot="title">
        <item :meta="item.meta" />
      </template>
      <!-- 二级导航选项 -->
      <sidebar-item
        v-for="child in item.children"
        :is-nest="true"
        :item="child"
        :key="child.path"
        :base-path="resolvePath(child.path)"
        class="nest-menu" />
    </el-submenu>

  </div>
</template>

<script>
import path from 'path'
import { isExternal } from '@/utils/validate'
import Item from './Item'
import AppLink from './Link'

export default {
  name: 'SidebarItem',
  components: { Item, AppLink },
  props: {
    // route object
    item: {
      type: Object,
      required: true
    },
    isNest: {
      type: Boolean,
      default: false
    },
    basePath: {
      type: String,
      default: ''
    }
  },
  data() {
    this.onlyOneChild = null
    return {}
  },
  methods: {
    hasOneShowingChild(children = [], parent) {
      const showingChildren = children.filter(item => {
        if (item.hidden) {
          return false
        } else {
          // Temp set(will be used if only has one showing child)
          this.onlyOneChild = item
          return true
        }
      })

      // When there is only one child router, the child router is displayed by default
      // 当只有一个子路由的时候，这个子路由默认被展示
      if (showingChildren.length === 1) {
        return true
      }

      // Show parent if there are no child router to display
      // 如果没有子路由，那就展示父路由
      if (showingChildren.length === 0) {
        this.onlyOneChild = { ... parent, path: '', noShowingChildren: true }
        return true
      }

      return false
    },
    resolvePath(routePath) {
      if (isExternal(routePath)) {
        return routePath
      }
      return path.resolve(this.basePath, routePath)
    }
  }
}
</script>
