<template>
  <div class="card">
    <div class="card-header">
      <div class="card-title">实时报警</div>
      <a-link>报警中心</a-link>
    </div>
    <div class="alert-list">
      <div class="alert-item" v-for="a in alerts" :key="a.id">
        <a-tag size="small" :color="a.color">{{ a.level }}</a-tag>
        <span class="alert-text">{{ a.text }}</span>
        <span class="alert-time">{{ a.time }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
const alerts = [
  { id: 1, level: '紧急', color: 'red', text: '3号车间主电机温度异常', time: '2 分钟前' },
  { id: 2, level: '严重', color: 'orangered', text: '装配线A气压低于阈值', time: '15 分钟前' },
  { id: 3, level: '警告', color: 'orange', text: '机器人#7 保养到期提醒', time: '32 分钟前' },
  { id: 4, level: '警告', color: 'orange', text: 'AGV#12 电池电量低于 20%', time: '1 小时前' },
  { id: 5, level: '严重', color: 'orangered', text: '立库区传感器通信中断', time: '2 小时前' }
]
</script>
