<template>
  <header class="header">
    <a-breadcrumb class="breadcrumb">
      <a-breadcrumb-item>首页</a-breadcrumb-item>
      <a-breadcrumb-item>设计中心</a-breadcrumb-item>
      <a-breadcrumb-item>设计概览</a-breadcrumb-item>
    </a-breadcrumb>

    <div class="search-input">
      <a-input placeholder="搜索项目 / 设备 / 文档" allow-clear>
        <template #prefix>
          <Icon name="search" />
        </template>
      </a-input>
    </div>

    <div class="header-actions">
      <div class="header-icon-btn" title="消息">
        <Icon name="message" />
      </div>
      <div class="header-icon-btn" title="日历">
        <Icon name="calendar" />
      </div>
      <div class="header-icon-btn" title="通知">
        <Icon name="bell" />
        <span class="badge-dot"></span>
      </div>
      <a-divider direction="vertical" :margin="0" style="height:20px" />
      <div class="user-info">
        <a-avatar :size="32" style="background:#4080ff">张</a-avatar>
        <div>
          <div class="user-name">张工程师</div>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
import Icon from './Icon.vue'
</script>
