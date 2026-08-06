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
          <el-icon><Folder /></el-icon>
          <span>项目管理</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'collaborate' }" @click="activeTab = 'collaborate'">
          <el-icon><UserFilled /></el-icon>
          <span>协同设计</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'comparison' }" @click="activeTab = 'comparison'">
          <el-icon><Grid /></el-icon>
          <span>方案比选</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'check' }" @click="activeTab = 'check'">
          <el-icon><CircleCheckFilled /></el-icon>
          <span>限额校验</span>
        </div>
        <div class="tab-item" :class="{ active: activeTab === 'collision' }" @click="activeTab = 'collision'">
          <el-icon><WarningFilled /></el-icon>
          <span>碰撞检测</span>
        </div>
      </nav>
      
      <div class="navbar-right">
        <span class="project-name">XXXX项目（B2层）</span>
        <div class="notification-btn">
          <el-icon><Bell /></el-icon>
        </div>
        <el-avatar :size="36" class="avatar">
          <img src="/建筑工人3D头像.png" alt="avatar" />
        </el-avatar>
      </div>
    </header>
    
    <!-- 左侧菜单 -->
    <el-aside
      :width="collapsed ? '64px' : '220px'"
      class="sidebar-aside"
    >
      <el-menu
        :default-active="selectedKey"
        mode="vertical"
        :collapse="collapsed"
        :collapse-transition="false"
        class="sidebar-menu"
        @select="handleMenuSelect"
      >
        <el-menu-item index="comparison">
          <el-icon><Grid /></el-icon>
          <template #title>优化方案比选</template>
        </el-menu-item>
      </el-menu>
      <!-- 折叠/展开按钮 -->
      <div
        class="sidebar-collapse-btn"
        :class="{ 'is-collapsed': collapsed }"
        @click="toggleCollapse"
      >
        <el-icon>
          <Fold v-if="!collapsed" />
          <Expand v-else />
        </el-icon>
      </div>
    </el-aside>
    
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
            <label class="form-label">环境</label>
            <el-radio-group v-model="environment" class="env-radio-group">
              <el-radio value="test">测试环境</el-radio>
              <el-radio value="prod">生产环境</el-radio>
            </el-radio-group>
          </div>
          
          <div class="form-row">
            <div class="form-item">
              <label class="form-label">
                <span class="required">*</span>
                <span>类目</span>
              </label>
              <el-select
                v-model="selectedCategories"
                multiple
                placeholder="请选择"
                class="category-select"
                clearable
                collapse-tags
                collapse-tags-tooltip
                :max-collapse-tags="1"
                popper-class="category-select-dropdown"
              >
                <template #header>
                  <el-checkbox
                    v-model="checkAll"
                    :indeterminate="indeterminate"
                    class="category-check-all"
                    @change="handleCheckAll"
                  >
                    全选
                  </el-checkbox>
                </template>
                <el-option
                  v-for="item in categoryOptions"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                />
              </el-select>
            </div>
          </div>
          
          <div class="form-actions">
            <el-button type="primary" :icon="Search" @click="handleSearch">
              查询商品
            </el-button>
            <el-button :icon="Refresh" @click="handleReset">
              重置
            </el-button>
          </div>
        </div>
        
        <!-- 数据表格 -->
        <div class="table-container">
          <el-table
            :data="tableData"
            :border="true"
            stripe
            class="data-table"
            height="calc(100vh - 420px)"
            :scrollbar-always-on="true"
            style="width: 100%"
          >
            <el-table-column type="index" label="序号" width="80" align="center" />
            <el-table-column prop="categoryName" label="类目名称" min-width="90" show-overflow-tooltip />
            <el-table-column prop="model" label="材料型号" min-width="220" show-overflow-tooltip />
            <el-table-column prop="spuModel" label="SPU型号" min-width="90" show-overflow-tooltip />
            <el-table-column prop="supplier" label="供应商名称" min-width="180" show-overflow-tooltip />
            <el-table-column prop="price" label="销售价（元）" width="140" sortable>
              <template #default="{ row }">
                <span class="price-text">{{ row.price.toLocaleString('zh-CN', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) }}</span>
              </template>
            </el-table-column>
          </el-table>
          
          <!-- 分页 -->
          <div class="pagination-container">
            <el-pagination
              v-model:current-page="currentPage"
              v-model:page-size="pageSize"
              :page-sizes="[10, 20, 50, 100]"
              :total="total"
              layout="total, prev, pager, next, sizes, jumper"
              :pager-count="5"
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              background
            />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import {
  Folder,
  UserFilled,
  Grid,
  CircleCheckFilled,
  WarningFilled,
  Bell,
  Search,
  Refresh,
  Fold,
  Expand
} from '@element-plus/icons-vue'

// 状态
const activeTab = ref('comparison')
const environment = ref('test')
const currentPage = ref(1)
const pageSize = ref(10)

// 响应式收缩状态
const collapsed = ref(false)
const selectedKey = ref('comparison')

// 计算主内容区左边距
const mainContentMargin = computed(() => collapsed.value ? 64 : 220)

// 类目选项与多选状态
const categoryOptions = [
  { value: '风机盘管', label: '风机盘管' },
  { value: '空调机组', label: '空调机组' },
  { value: '新风系统', label: '新风系统' },
  { value: '通风设备', label: '通风设备' },
  { value: '阀门管件', label: '阀门管件' }
]
const selectedCategories = ref([])
const checkAll = ref(false)
const indeterminate = ref(false)

watch(selectedCategories, (val) => {
  if (val.length === 0) {
    checkAll.value = false
    indeterminate.value = false
  } else if (val.length === categoryOptions.length) {
    checkAll.value = true
    indeterminate.value = false
  } else {
    indeterminate.value = true
  }
})

function handleCheckAll(val) {
  indeterminate.value = false
  if (val) {
    selectedCategories.value = categoryOptions.map((item) => item.value)
  } else {
    selectedCategories.value = []
  }
}

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

// 方法
function handleSearch() {
  console.log('搜索:', { environment: environment.value, categories: selectedCategories.value })
}

function handleReset() {
  environment.value = 'test'
  selectedCategories.value = []
  currentPage.value = 1
}

function handleSizeChange(val) {
  pageSize.value = val
  currentPage.value = 1
  console.log('每页条数:', val)
}

function handleCurrentChange(val) {
  console.log('当前页:', val)
}

function handleMenuSelect(index) {
  selectedKey.value = index
}

function toggleCollapse() {
  collapsed.value = !collapsed.value
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
  color: #409eff;
  border-bottom: 2px solid #409eff;
}

.tab-item :deep(.el-icon) {
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
.sidebar-aside {
  position: fixed;
  top: 60px;
  left: 0;
  bottom: 0;
  z-index: 50;
  overflow: hidden;
  background: #fff;
}

.sidebar-menu {
  height: 100%;
  border-right: none;
}

.sidebar-menu:not(.el-menu--collapse) {
  width: 220px;
}

/* 菜单选中项样式 */
.sidebar-menu :deep(.el-menu-item.is-active) {
  background-color: rgba(64, 158, 255, 0.08) !important;
  color: #409eff !important;
}

/* 折叠/展开按钮 */
.sidebar-collapse-btn {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding: 0 16px;
  cursor: pointer;
  color: #4e5969;
  font-size: 16px;
  border-top: 1px solid #e5e6eb;
  background: #ffffff;
  transition: color 0.2s, background 0.2s;
}

.sidebar-collapse-btn.is-collapsed {
  justify-content: center;
  padding: 0;
}

.sidebar-collapse-btn:hover {
  color: #409eff;
  background: #f2f3f5;
}

.sidebar-collapse-btn :deep(.el-icon) {
  font-size: 18px;
}

/* 主内容区 */
.main-content {
  position: fixed;
  top: 60px;
  left: v-bind(mainContentMargin + 16 + 'px');
  right: 16px;
  bottom: 0;
  margin-left: 0;
  margin-top: 0;
  margin-right: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: left 0.2s;
}

/* 标题卡片 */
.title-card {
  background: #ffffff;
  border-radius: 4px;
  padding: 8px 24px;
  margin: 16px 0;
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
  color: #409eff;
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
  margin: 0 0 16px 0;
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
  min-width: 0;
}

.form-label {
  width: 98px;
  font-size: 14px;
  color: rgba(29, 33, 41, 1);
  line-height: 32px;
  flex-shrink: 0;
}

.required {
  color: #f56c6c;
  margin-right: 2px;
}

/* 类目多选框：占满 form-item 剩余空间，与表格右边缘对齐 */
.category-select {
  display: block !important;
  width: 100% !important;
  min-width: 0 !important;
  max-width: none !important;
  flex: 1 1 auto;
}

/* 下拉菜单中的"全选"项样式 */
:deep(.category-select-dropdown) .category-check-all {
  display: flex;
  width: 100%;
  height: unset;
  padding: 4px 12px;
  box-sizing: border-box;
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

/* 表格 */
.table-container {
  margin-top: 20px;
  border-radius: 4px;
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
}

.data-table :deep(.el-table__header th) {
  background: #f5f7fa;
  font-weight: 500;
  color: #1d2129;
}

.data-table :deep(.el-table__cell) {
  color: #1d2129;
}

.data-table :deep(.el-table__row:hover > td) {
  background: #f5f7fa !important;
}

.price-text {
  color: #409eff;
}

/* 分页 */
.pagination-container {
  display: flex;
  justify-content: flex-end;
  padding: 12px 0;
  background: #ffffff;
  border-top: 1px solid #ebeef5;
  flex-shrink: 0;
}
</style>
