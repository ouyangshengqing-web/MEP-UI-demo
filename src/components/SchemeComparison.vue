<template>
  <div class="scheme-comparison-page">
    <!-- 顶部导航栏 -->
    <header class="navbar">
      <div class="navbar-left">
        <div class="logo">
          <div class="logo-icon">
            <img src="/中建logo.svg" alt="logo" class="logo-img" />
          </div>
          <span class="logo-text">机电智能设计系统</span>
        </div>
      </div>
      
      <nav class="navbar-tabs">
        <div class="tab-item" :class="{ active: activeTab === 'project' }" @click="activeTab = 'project'">
          <Icon name="folder" />
          <span>项目管理</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'collaborate' }" @click="activeTab = 'collaborate'">
          <Icon name="team" />
          <span>协同设计</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'comparison' }" @click="activeTab = 'comparison'">
          <Icon name="apps" />
          <span>方案比选</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'check' }" @click="activeTab = 'check'">
          <Icon name="check-circle" />
          <span>限额校验</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'collision' }" @click="activeTab = 'collision'">
          <Icon name="alert-circle" />
          <span>碰撞检测</span>
        </div>
      </nav>
      
      <div class="navbar-right">
        <span class="project-name">XXXX项目（B2层）</span>
        <div class="notification-btn">
          <Icon name="notification" />
        </div>
        <a-avatar :size="36" class="avatar">
          <img src="/建筑工人3D头像.png" alt="avatar" />
        </a-avatar>
      </div>
    </header>
    
    <!-- 左侧菜单 -->
    <a-layout-sider
      v-model:collapsed="collapsed"
      :breakpoint="'xl'"
      :width="220"
      :collapsed-width="48"
      :hide-trigger="true"
      :collapsible="true"
      class="sidebar-sider"
    >
      <a-menu
        v-model:selected-keys="selectedKeys"
        v-model:collapsed="collapsed"
        mode="vertical"
        :show-collapse-button="true"
        class="sidebar-menu"
      >
        <a-menu-item key="comparison">
          <template #icon><Icon name="apps" /></template>
          <span>优化方案比选</span>
        </a-menu-item>
      </a-menu>
    </a-layout-sider>
    
    <!-- 主内容区 -->
    <main class="main-content">
      <!-- 标题区域 -->
      <div class="title-card">
        <div class="title-image">
          <img src="/banner1.jpg" alt="banner" />
        </div>
        <div class="title-content">
          <h1 class="title">优化方案比选分析</h1>
          <p class="subtitle">多方案智能对比分析，结合EPC商品数据辅助方案决策</p>
        </div>
      </div>
      
      <!-- 查询条件区 -->
      <div class="query-card">
        <div class="query-title">查询条件</div>
        <div class="query-form">
          <div class="form-row">
            <label class="form-label" style="padding-left: 6px;">环境</label>
            <a-radio-group v-model="environment" class="env-radio-group">
              <a-radio value="test">测试环境</a-radio>
              <a-radio value="prod">生产环境</a-radio>
            </a-radio-group>
          </div>
          
          <div class="form-row">
            <div class="form-item">
              <label class="form-label">
                <span class="required">*</span>
                <span>类目</span>
              </label>
              <a-select
                v-model="selectedCategories"
                mode="multiple"
                placeholder="请选择"
                class="category-select"
                allow-clear
              >
                <a-option value="风机盘管">风机盘管</a-option>
                <a-option value="空调机组">空调机组</a-option>
                <a-option value="新风系统">新风系统</a-option>
                <a-option value="通风设备">通风设备</a-option>
                <a-option value="阀门管件">阀门管件</a-option>
              </a-select>
            </div>
          </div>
          
          <div class="form-actions">
            <a-button type="primary" class="search-btn" @click="handleSearch">
              <template #icon><Icon name="search" /></template>
              查询商品
            </a-button>
            <a-button class="reset-btn" @click="handleReset">
              <template #icon><Icon name="refresh" /></template>
              重置
            </a-button>
          </div>
        </div>
        
        <!-- 数据表格 -->
        <div class="table-container">
          <a-table
            :columns="columns"
            :data="tableData"
            :pagination="false"
            :scroll="{ x: '100%', y: '100%' }"
            :bordered="{ cell: true }"
            stripe
            class="data-table"
          />
          
          <!-- 分页 -->
          <div class="pagination-container">
            <div class="pagination-info">共 {{ total }} 条</div>
            <div class="pagination">
              <div class="page-btn prev" :class="{ disabled: currentPage === 1 }">
                <Icon name="left" />
              </div>
              <div 
                v-for="page in visiblePages" 
                :key="page"
                class="page-number"
                :class="{ active: page === currentPage, ellipsis: page === '...' }"
                @click="page !== '...' && goToPage(page)"
              >
                {{ page }}
              </div>
              <div class="page-btn next" :class="{ disabled: currentPage === totalPages }">
                <Icon name="right" />
              </div>
              <div class="page-size-selector">
                <span>10条/页</span>
                <Icon name="down" />
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import Icon from './Icon.vue'

// 状态
const activeTab = ref('comparison')
const environment = ref('test')
const selectedCategories = ref([])
const currentPage = ref(1)
const pageSize = ref(10)

// 响应式收缩状态
const collapsed = ref(false)
const selectedKeys = ref(['comparison'])

// 计算主内容区左边距
const mainContentMargin = computed(() => collapsed.value ? 48 : 220)

// 表格列配置
const columns = [
  {
    title: '序号',
    dataIndex: 'index',
    width: 80,
    align: 'center'
  },
  {
    title: '类目名称',
    dataIndex: 'categoryName',
    width: 160,
    ellipsis: true,
    tooltip: true
  },
  {
    title: '材料型号',
    dataIndex: 'model',
    width: 270,
    ellipsis: true,
    tooltip: true
  },
  {
    title: 'SPU型号',
    dataIndex: 'spuModel',
    width: 140,
    ellipsis: true,
    tooltip: true
  },
  {
    title: '供应商名称',
    dataIndex: 'supplier',
    width: 180,
    ellipsis: true,
    tooltip: true
  },
  {
    title: '销售价（元）',
    dataIndex: 'price',
    width: 100,
    ellipsis: true,
    tooltip: true,
    sortable: {
      sortDirections: ['ascend', 'descend']
    },
    bodyCellStyle: {
      color: '#165DFF'
    },
    render: ({ record }) =>
      record.price.toLocaleString('zh-CN', {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2
      })
  }
]

// 示例数据
const tableData = ref([
  { index: 1, categoryName: '卧式暗装风机盘管', model: '余压：12Pa', spuModel: '-', supplier: '深圳矩材汇供应链管理有限公司', price: 4125.80 },
  { index: 2, categoryName: '卧式暗装风机盘管', model: '余压:30Pa 输入功率:151W 冷量:7650W 风量:1020m³/h', spuModel: '型号:JFP-136', supplier: '武汉天鹏暖通空调工程有限责任公司', price: 3692.65 },
  { index: 3, categoryName: '卧式暗装风机盘管', model: '余压:30Pa 输入功率:105W 冷量:5650W 风量:850m³/h', spuModel: '型号:JFP-102', supplier: '武汉天鹏暖通空调工程有限责任公司', price: 3074.20 },
  { index: 4, categoryName: '卧式暗装风机盘管', model: '余压:50 输入功率:245 静压:51.5dB 热量:6850W', spuModel: '-', supplier: '北京德尔力通科技发展有限公司', price: 2209.00 },
  { index: 5, categoryName: '卧式暗装风机盘管', model: '输入功率:172 静压:50Pa 热量:5333 冷量:5140W', spuModel: '-', supplier: '北京德尔力通科技发展有限公司', price: 1662.00 },
  { index: 6, categoryName: '卧式暗装风机盘管', model: '风量:2380m²/h 冷量:1.3KW', spuModel: '型号:014305D', supplier: '四川斯考特科技有限公司', price: 1661.58 },
  { index: 7, categoryName: '卧式暗装风机盘管', model: '余压:50 输入功率:150 静压:50Pa 热量:4175 冷量:5...', spuModel: '-', supplier: '北京德尔力通科技发展有限公司', price: 1498.00 },
  { index: 8, categoryName: '双层百叶风口', model: '规格:2000*2000', spuModel: '-', supplier: '北京钜金机电设备有限公司', price: 1412.81 },
  { index: 9, categoryName: '卧式暗装风机盘管', model: '余压:30Pa 输入功率:171W 热量:15.91KW 冷量:12.5KW', spuModel: '-', supplier: '四川正科通风空调设备安装工程有限公司', price: 1408.66 },
  { index: 10, categoryName: '卧式暗装风机盘管', model: '余压:30Pa 输入功率:151W 冷量:7650W 风量:1020m³/h', spuModel: '型号:FP-136', supplier: '武汉天鹏暖通空调工程有限责任公司', price: 1378.45 }
])

// 总数据条数（用于分页）
const total = ref(93)

// 总页数
const totalPages = computed(() => Math.ceil(total.value / pageSize.value))

// 可见页码
const visiblePages = computed(() => {
  const pages = []
  const total = totalPages.value
  const current = currentPage.value
  
  if (total <= 7) {
    for (let i = 1; i <= total; i++) {
      pages.push(i)
    }
  } else {
    if (current <= 4) {
      for (let i = 1; i <= 5; i++) pages.push(i)
      pages.push('...')
      pages.push(total)
    } else if (current >= total - 3) {
      pages.push(1)
      pages.push('...')
      for (let i = total - 4; i <= total; i++) pages.push(i)
    } else {
      pages.push(1)
      pages.push('...')
      for (let i = current - 1; i <= current + 1; i++) pages.push(i)
      pages.push('...')
      pages.push(total)
    }
  }
  return pages
})

// 方法
function handleSearch() {
  console.log('搜索:', { environment: environment.value, categories: selectedCategories.value })
}

function handleReset() {
  environment.value = 'test'
  selectedCategories.value = []
}

function handleOptional(record) {
  console.log('选择:', record)
}

function goToPage(page) {
  currentPage.value = page
}
</script>

<style scoped>
.scheme-comparison-page {
  display: flex;
  min-height: 100vh;
  background: #f7f8fa;
  font-family: 'PingFang SC', -apple-system, BlinkMacSystemFont, sans-serif;
}

/* 顶部导航栏 */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 60px;
  background: #ffffff;
  border-bottom: 1px solid #e5e6eb;
  display: flex;
  align-items: center;
  padding: 0 16px;
  z-index: 100;
}

.navbar-left {
  display: flex;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo-icon {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.logo-img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.logo-text {
  font-family: 'DingTalk JinBuTi', 'PingFang SC', sans-serif;
  font-size: 20px;
  color: rgba(29, 33, 41, 1);
  font-weight: 400;
  font-style: italic;
  background-clip: unset;
  -webkit-background-clip: unset;
}

.navbar-tabs {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 32px;
}

.tab-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 9px 8px;
  border-radius: 2px;
  cursor: pointer;
  color: #4e5969;
  font-size: 14px;
  transition: all 0.2s;
}

.tab-item:hover {
  background: rgba(242, 243, 245, 0);
}

.tab-item:hover {
  background: #f2f3f5;
}

.tab-item.active {
  color: #165dff;
  border-bottom: 2px solid #165dff;
}

.tab-item :deep(.icon) {
  font-size: 14px;
}

.navbar-right {
  margin-left: auto;
  display: flex;
  align-items: center;
  gap: 16px;
}

.project-name {
  font-size: 14px;
  color: #4e5969;
  text-align: right;
}

.notification-btn {
  width: 36px;
  height: 36px;
  background: #f2f3f5;
  border-radius: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background 0.2s;
}

.notification-btn:hover {
  background: #e5e6eb;
}

.avatar {
  border-radius: 100px;
  overflow: hidden;
}

/* 左侧菜单 */
/* Arco Layout Sider */
.sidebar-sider {
  position: fixed;
  top: 60px;
  left: 0;
  bottom: 0;
  z-index: 50;
  overflow: hidden;
}

.sidebar-sider :deep(.arco-layout-sider-children) {
  overflow: hidden;
}

.sidebar-menu {
  height: 100%;
  width: 100% !important;
}

/* 菜单折叠时隐藏文字，图标居中 */
.sidebar-menu:deep(.arco-menu-collapsed) {
  .arco-menu-title {
    display: none;
  }
  .arco-menu-item {
    justify-content: center;
    padding: 0 !important;
  }
}

/* 菜单选中项样式 */
.sidebar-menu :deep(.arco-menu-item.arco-menu-selected) {
  background-color: rgba(22, 93, 255, 0.08) !important;
  color: #165dff !important;
}

.sidebar-menu :deep(.arco-menu-item.arco-menu-selected .arco-icon),
.sidebar-menu :deep(.arco-menu-item.arco-menu-selected .arco-icon svg) {
  color: #165dff !important;
  fill: #165dff !important;
}

/* 主内容区 */
.main-content {
  flex: 1;
  min-width: 0;
  margin-left: v-bind(mainContentMargin + 'px');
  margin-top: 60px;
  margin-right: 16px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  height: calc(100vh - 60px);
  overflow: hidden;
  transition: margin-left 0.2s;
}

/* 标题卡片 */
.title-card {
  background: #ffffff;
  border-radius: 4px;
  padding: 8px 24px;
  margin-bottom: 16px;
  display: flex;
  align-items: center;
  gap: 16px;
  height: 86px;
}

.title-image {
  width: 100px;
  height: 70px;
  flex-shrink: 0;
}

.title-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 2px;
}

.title-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.title {
  font-family: 'DingTalk JinBuTi', 'PingFang SC', sans-serif;
  font-size: 24px;
  color: #165dff;
  font-style: italic;
  margin: 0 0 4px 0;
  font-weight: normal;
}

.subtitle {
  font-size: 14px;
  color: #1d2129;
  margin: 0;
}

/* 查询卡片 */
.query-card {
  background: #ffffff;
  border-radius: 4px;
  padding: 11px 20px 31px;
  min-width: 0;
  flex: 1;
  min-height: 0;
  display: flex;
  flex-direction: column;
}

.query-title {
  font-size: 16px;
  font-weight: 500;
  color: #1d2129;
  margin-bottom: 20px;
}

.query-form {
  padding-left: 0;
}

/* 表单项 */
.form-row {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
}

.form-item {
  display: flex;
  align-items: center;
  width: 100%;
  flex: 1;
}

.form-label {
  width: 98px;
  font-size: 14px;
  color: rgba(29, 33, 41, 1);
  line-height: 32px;
  flex-shrink: 0;
}

.required {
  color: #f53f3f;
  margin-right: 2px;
}

.category-select {
  display: block !important;
  width: 100% !important;
  max-width: 800px !important;
}

.category-select :deep(.arco-select-view) {
  width: 100% !important;
  min-width: 0 !important;
}

.env-radio-group {
  display: flex;
  gap: 24px;
}

.form-actions {
  display: flex;
  gap: 8px;
  margin-top: 16px;
  padding-left: 98px;
}

.search-btn {
  background: #165dff !important;
  border-color: #165dff !important;
}

.search-btn :deep(.icon) {
  font-size: 14px;
}

.reset-btn {
  color: #4e5969;
}

/* 表格 */
.table-container {
  margin-top: 20px;
  border: 1px solid #e5e6eb;
  border-radius: 2px;
  display: flex;
  flex-direction: column;
  min-width: 0;
  min-height: 0;
  flex: 1;
  overflow: hidden;
}

.data-table {
  font-size: 14px;
  min-width: 0;
  flex: 1;
  min-height: 0;
}

.data-table :deep(.arco-table-th) {
  background: #f2f3f5;
  font-weight: 500;
  color: #1d2129;
}

.data-table :deep(.arco-table-td) {
  color: #1d2129;
}

.data-table :deep(.arco-table-tr:hover .arco-table-td) {
  background: #f2f3f5 !important;
}

/* 分页 */
.pagination-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 8px;
  padding: 12px 16px;
  background: #ffffff;
  border-top: 1px solid #e5e6eb;
  flex-shrink: 0;
}

.pagination-info {
  font-size: 14px;
  color: #1d2129;
}

.pagination {
  display: flex;
  align-items: center;
  gap: 8px;
}

.page-btn,
.page-number {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 24px;
  height: 24px;
  padding: 0 4px;
  border-radius: 2px;
  cursor: pointer;
  font-size: 14px;
  color: #4e5969;
  transition: all 0.2s;
}

.page-btn:hover:not(.disabled),
.page-number:hover:not(.ellipsis) {
  background: #f2f3f5;
}

.page-number.active {
  background: #e8f3ff;
  color: #165dff;
}

.page-btn.disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.page-size-selector {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 4px 12px;
  background: #f2f3f5;
  border-radius: 2px;
  font-size: 14px;
  color: #1d2129;
  margin-left: 8px;
  cursor: pointer;
}

.page-size-selector :deep(.icon) {
  font-size: 12px;
}
</style>
