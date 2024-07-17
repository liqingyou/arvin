<script setup lang="ts">
import { onMounted } from 'vue'
// Echarts 为init（dom元素后的类型）
// EChartsOption 为 option 的类型
import type { ECharts, EChartsOption } from 'echarts'
import { init } from 'echarts'
import axios from 'axios'

interface TjOnline {
  date: string
  onlineNum: number
  google: number
  apple: number
  jl: number
}

const chartData = ref([])

async function fetchContent() {
  try {
    const { data } = await axios.get('https://m3test.2loveyou.com/spin/test/player_read/online_data?limit=50&key=tj_online_1')
    chartData.value = data.result.reverse().map((item: TjOnline) => item)
    updateChart() // Call updateChart after fetching new data
  }
  catch (error) {
    console.error('Error fetching profile content:', error)
  }
}

function updateChart() {
  const charEch: ECharts = init(document.getElementById('char-cho-zhi') as HTMLElement, 'dark')
  const option: EChartsOption = {
    darkMode: true,
    title: {
      text: 'Income Line',
    },
    tooltip: {
      trigger: 'axis',
    },
    legend: {
      data: ['google', 'apple', 'jl'],
    },
    xAxis: {
      type: 'category',
      data: chartData.value.map((item: TjOnline) => item.date),
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        data: chartData.value.map((item: TjOnline) => item.google),
        name: 'google',
        type: 'line',
        stack: 'Total',
      },
      {
        data: chartData.value.map((item: TjOnline) => item.apple),
        name: 'apple',
        type: 'line',
        stack: 'Total',
      },
      {
        data: chartData.value.map((item: TjOnline) => item.jl),
        name: 'jl',
        type: 'line',
        stack: 'Total',
      },
    ],
  }
  charEch.setOption(option)
}

onMounted(async () => {
  await fetchContent() // Initial fetch when component is mounted

  // Set up interval to fetch new data every minute
  setInterval(async () => {
    await fetchContent()
  }, 300000) // 60000 milliseconds = 1 minute
})
</script>

<template>
  <ShadowCard class="justify-between !p-[5px]">
    <div id="char-cho-zhi" class="char-cls" />
  </ShadowCard>
</template>

<style scoped>
.char-cls {
  z-index: 100;
  width: 100%;
  height: auto;
}
</style>
