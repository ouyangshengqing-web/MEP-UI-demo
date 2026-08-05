<template>
  <div class="card">
    <div class="card-header">
      <div class="card-title">年度项目趋势</div>
      <div class="chart-actions">
        <button
          v-for="t in tabs"
          :key="t"
          class="chart-tab"
          :class="{ active: active === t }"
          @click="active = t"
        >{{ t }}</button>
      </div>
    </div>
    <div class="chart-legend" style="margin-bottom:12px">
      <div class="chart-legend-item" v-for="(s, i) in series" :key="s.name">
        <span class="dot" :style="{ background: s.color }"></span>
        <span>{{ s.name }}</span>
      </div>
    </div>
    <div ref="chartEl" class="chart-box"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, onBeforeUnmount, nextTick } from 'vue'
import * as echarts from 'echarts'

const tabs = ['月度', '季度', '年度']
const active = ref('月度')
const chartEl = ref(null)
let chart = null

const months = ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月']
const series = [
  { name: '项目总数', color: '#4080ff', data: [120, 132, 101, 134, 90, 230, 210, 182, 191, 234, 290, 330] },
  { name: '已完成', color: '#00b42a', data: [82, 93, 70, 95, 60, 170, 150, 130, 140, 175, 210, 240] },
  { name: '进行中', color: '#ff7d00', data: [25, 28, 22, 30, 20, 45, 40, 35, 38, 42, 55, 65] },
  { name: '延期', color: '#f53f3f', data: [13, 11, 9, 9, 10, 15, 20, 17, 13, 17, 25, 25] }
]

const render = () => {
  if (!chart) return
  chart.setOption({
    grid: { left: 40, right: 16, top: 16, bottom: 30 },
    tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
    legend: { show: false },
    xAxis: {
      type: 'category',
      data: months,
      axisLine: { lineStyle: { color: '#e5e6eb' } },
      axisLabel: { color: '#86909c', fontSize: 12 },
      axisTick: { show: false }
    },
    yAxis: {
      type: 'value',
      splitLine: { lineStyle: { color: '#f2f3f5' } },
      axisLabel: { color: '#86909c', fontSize: 12 }
    },
    series: series.map((s, i) => ({
      name: s.name,
      type: 'bar',
      stack: 'total',
      barWidth: 14,
      itemStyle: { color: s.color, borderRadius: i === series.length - 1 ? [4, 4, 0, 0] : 0 },
      data: s.data
    }))
  }, true)
}

onMounted(async () => {
  await nextTick()
  chart = echarts.init(chartEl.value)
  render()
  window.addEventListener('resize', resize)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', resize)
  chart?.dispose()
})
const resize = () => chart?.resize()
watch(active, render)
</script>
