<template>
  <div class="pareto-container">
    <div ref="chartRef" style="width: 800px; height: 500px;"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, watchEffect } from 'vue'
import * as echarts from 'echarts'

// 模拟数据
const chartData = ref([
  { name: '缺陷A', value: 45 },
  { name: '缺陷B', value: 30 },
  { name: '缺陷C', value: 15 },
  { name: '缺陷D', value: 7 },
  { name: '缺陷E', value: 3 }
])

const chartRef = ref(null)

// 处理数据生成帕雷托数据
const processData = (data) => {
  // 按值降序排序
  const sortedData = [...data].sort((a, b) => b.value - a.value)
  
  // 计算累计百分比
  const total = sortedData.reduce((sum, item) => sum + item.value, 0)
  let cumulative = 0
  return sortedData.map(item => {
    cumulative += item.value
    return {
      name: item.name,
      value: item.value,
      percentage: (cumulative / total * 100).toFixed(1)
    }
  })
}

// 初始化图表
const initChart = () => {
  const myChart = echarts.init(chartRef.value)
  
  const processedData = processData(chartData.value)
  
  const option = {
    title: {
      text: '产品质量问题帕雷托分析',
      left: 'center'
    },
    tooltip: {
      trigger: 'axis',
      formatter: params => {
        const data = params[1].data
        return `${data.name}<br/>
                数量: ${data.value}<br/>
                累计占比: ${data.percentage}%`
      }
    },
    xAxis: [{
      type: 'category',
      data: processedData.map(item => item.name),
      axisLabel: {
        rotate: 45
      }
    }],
    yAxis: [
      {
        type: 'value',
        name: '问题数量',
        min: 0,
        max: Math.max(...processedData.map(item => item.value)) * 1.2,
        axisLine: {
          lineStyle: {
            color: '#5470C6'
          }
        }
      },
      {
        type: 'value',
        name: '累计百分比',
        min: 0,
        max: 100,
        axisLabel: {
          formatter: '{value}%'
        },
        axisLine: {
          lineStyle: {
            color: '#91CC75'
          }
        }
      }
    ],
    series: [
      {
        name: '问题数量',
        type: 'bar',
        data: processedData.map(item => item.value),
        itemStyle: {
          color: '#5470C6'
        },
        emphasis: {
          focus: 'series'
        }
      },
      {
        name: '累计百分比',
        type: 'line',
        yAxisIndex: 1,
        data: processedData.map(item => item.percentage),
        symbol: 'circle',
        symbolSize: 8,
        itemStyle: {
          color: '#91CC75'
        },
        lineStyle: {
          width: 3
        }
      }
    ],
    grid: {
      top: '15%',
      right: '5%',
      bottom: '20%'
    }
  }

  myChart.setOption(option)
  
  // 窗口大小改变时自适应
  window.addEventListener('resize', () => myChart.resize())
}

onMounted(() => {
  initChart()
})

watchEffect(() => {
  if (chartRef.value) {
    initChart()
  }
})
</script>

<style>
.pareto-container {
  padding: 20px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0,0,0,0.1);
}
</style>