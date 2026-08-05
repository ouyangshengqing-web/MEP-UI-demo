<template>
  <div class="card">
    <div class="card-header">
      <div class="card-title">团队成员</div>
      <a-link>查看团队</a-link>
    </div>
    <div class="member-list">
      <div class="member-item" v-for="m in members" :key="m.name">
        <a-avatar :size="36" :style="{ background: m.color }">{{ m.name.charAt(0) }}</a-avatar>
        <div class="meta">
          <div class="name">{{ m.name }}</div>
          <div class="role">{{ m.role }}</div>
        </div>
        <span class="status-dot" :style="{ background: m.online ? '#00b42a' : '#c9cdd4' }"></span>
      </div>
    </div>
  </div>
</template>

<script setup>
const members = [
  { name: '李伟', role: '机械工程师', color: '#4080ff', online: true },
  { name: '王芳', role: '电气工程师', color: '#ff7d00', online: true },
  { name: '张敏', role: '仿真分析师', color: '#00b42a', online: true },
  { name: '刘强', role: '项目经理', color: '#722ed1', online: false },
  { name: '陈晓明', role: '运维工程师', color: '#f53f3f', online: true }
]
</script>
