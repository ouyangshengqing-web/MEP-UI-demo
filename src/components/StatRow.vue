<template>
  <div class="stat-row">
    <div class="stat-card" v-for="item in stats" :key="item.label">
      <div class="stat-icon" :class="item.color">
        <Icon :name="item.icon" />
      </div>
      <div class="stat-body">
        <div class="stat-label">{{ item.label }}</div>
        <div class="stat-value">{{ item.value }}</div>
        <div class="stat-delta" :class="item.trend">
          <Icon :name="item.trend === 'up' ? 'arrowUp' : 'arrowDown'" :size="12" />
          较上月 {{ item.delta }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Icon from './Icon.vue'

const stats = [
  { label: '设备总数', value: '1,247', delta: '+12.5%', trend: 'up', color: 'blue', icon: 'device' },
  { label: '运行中', value: '856', delta: '+8.2%', trend: 'up', color: 'green', icon: 'power' },
  { label: '维护中', value: '47', delta: '-3.1%', trend: 'down', color: 'orange', icon: 'wrench' },
  { label: '故障', value: '12', delta: '-15.0%', trend: 'down', color: 'red', icon: 'warning' }
]
</script>
