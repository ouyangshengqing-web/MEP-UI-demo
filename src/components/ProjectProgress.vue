<template>
  <div class="card">
    <div class="card-header">
      <div class="card-title">项目进度</div>
      <a-link>全部项目</a-link>
    </div>
    <div class="project-list">
      <div class="project-item" v-for="p in list" :key="p.name">
        <div class="project-item-head">
          <span class="project-name">{{ p.name }}</span>
          <a-tag size="small" :color="p.statusColor">{{ p.status }}</a-tag>
        </div>
        <a-progress :percent="p.percent / 100" :show-text="false" :stroke-width="6" :color="p.statusColor" />
        <div class="project-item-head">
          <span style="color:#86909c;font-size:12px">计划 {{ p.plan }} 天</span>
          <span class="project-pct">{{ p.percent }}%</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const list = [
  { name: '智能装配线A', plan: 90, percent: 82, status: '进行中', statusColor: 'arcoblue' },
  { name: '机器人控制柜', plan: 60, percent: 65, status: '进行中', statusColor: 'arcoblue' },
  { name: 'AGV调度系统', plan: 45, percent: 100, status: '已完成', statusColor: 'green' },
  { name: '视觉检测模块', plan: 30, percent: 45, status: '延期', statusColor: 'red' },
  { name: '仓储立库升级', plan: 120, percent: 28, status: '规划中', statusColor: 'orange' }
]
</script>
