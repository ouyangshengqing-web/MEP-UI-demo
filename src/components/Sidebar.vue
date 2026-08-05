<template>
  <aside class="sidebar">
    <div class="sidebar-logo">
      <div class="logo-icon">ME</div>
      <div class="logo-text">机电智能一体化</div>
    </div>
    <div class="sidebar-menu">
      <div class="menu-section">主菜单</div>
      <div
        v-for="item in mainMenu"
        :key="item.key"
        class="menu-item"
        :class="{ active: active === item.key }"
        @click="active = item.key"
      >
        <Icon :name="item.icon" />
        <span>{{ item.label }}</span>
      </div>
      <div class="menu-section">系统</div>
      <div
        v-for="item in systemMenu"
        :key="item.key"
        class="menu-item"
        :class="{ active: active === item.key }"
        @click="active = item.key"
      >
        <Icon :name="item.icon" />
        <span>{{ item.label }}</span>
      </div>
    </div>
  </aside>
</template>

<script setup>
import { ref } from 'vue'
import Icon from './Icon.vue'

const active = ref('dashboard')
const mainMenu = [
  { key: 'dashboard', label: '设计概览', icon: 'dashboard' },
  { key: 'project', label: '项目管理', icon: 'folder' },
  { key: 'model', label: '设备建模', icon: 'cube' },
  { key: 'electric', label: '电气设计', icon: 'bolt' },
  { key: 'simulate', label: '仿真分析', icon: 'chart' },
  { key: 'maintain', label: '运维管理', icon: 'wrench' }
]
const systemMenu = [
  { key: 'team', label: '团队协作', icon: 'team' },
  { key: 'alarm', label: '报警中心', icon: 'bell' },
  { key: 'report', label: '报表分析', icon: 'report' },
  { key: 'config', label: '系统设置', icon: 'setting' }
]
</script>
