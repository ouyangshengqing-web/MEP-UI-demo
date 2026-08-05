# MEP-UI-demo

机电智能一体化设计系统 Demo，基于 **Vue 3 + Vite + ECharts** 构建。

## 功能模块

- **设计概览仪表板**：4 个核心指标卡片（设备总数 / 运行中 / 维护中 / 故障）
- **年度项目趋势**：堆叠柱状图，支持月度 / 季度 / 年度切换
- **设备状态分布**：环形图 + 图例展示
- **项目进度**：进度列表 + 状态标签
- **团队成员**：在线状态指示
- **实时报警**：紧急 / 严重 / 警告三级报警

## 技术栈

- Vue 3
- Vite
- ECharts
- Vue Router（可选扩展）

## 启动项目

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

访问 http://localhost:5173 查看效果。

## 项目结构

```
src/
├── components/          # 组件目录
│   ├── Header.vue        # 顶部导航栏
│   ├── Sidebar.vue       # 侧边栏
│   ├── StatRow.vue       # 统计卡片行
│   ├── DeviceStatusChart.vue   # 设备状态图表
│   ├── ProjectTrendChart.vue   # 项目趋势图表
│   ├── ProjectProgress.vue     # 项目进度
│   ├── RealTimeAlerts.vue      # 实时报警
│   └── TeamMembers.vue         # 团队成员
├── styles/
│   └── global.css        # 全局样式
├── App.vue               # 根组件
└── main.js               # 入口文件
```
