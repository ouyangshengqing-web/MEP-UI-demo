<template>
  <div class="card">
    <div class="card-header">
      <div class="card-title">设备状态分布</div>
      <a-link>查看详情</a-link>
    </div>
    <div class="donut-wrap">
      <div ref="chartEl" class="donut-chart"></div>
      <div class="donut-legend">
        <div class="donut-legend-item" v-for="item in data" :key="item.name">
          <div class="lbl">
            <span class="dot" :style="{ background: item.color }"></span>
            <span>{{ item.name }}</span>
          </div>
          <div class="val">{{ item.value }} 台</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue'
import * as echarts from 'echarts'

const chartEl = ref(null)
let chart = null

const data = [
  { name: '正常运行', value: 856, color: '#00b42a' },
  { name: '待机', value: 232, color: '#4080ff' },
  { name: '维护中', value: 47, color: '#ff7d00' },
  { name: '故障', value: 12, color: '#f53f3f' }
]

onMounted(async () => {
  await nextTick()
  chart = echarts.init(chartEl.value)
  chart.setOption({
    tooltip: { trigger: 'item' },
    series: [{
      type: 'pie',
      radius: ['62%', '85%'],
      avoidLabelOverlap: false,
      label: { show: false },
      labelLine: { show: false },
      data: data.map(d => ({ value: d.value, name: d.name, itemStyle: { color: d.color } }))
    }],
    graphic: [
      { type: 'text', left: 'center', top: '38%', style: { text: '1,147', fill: '#1d2129', fontSize: 22, fontWeight: 600, textAlign: 'center' } },
      { type: 'text', left: 'center', top: '52%', style: { text: '总设备数', fill: '#86909c', fontSize: 12, textAlign: 'center' } }
    ]
  })
  window.addEventListener('resize', resize)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', resize)
  chart?.dispose()
})
const resize = () => chart?.resize()
</script>
