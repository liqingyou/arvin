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
    const { data } = await axios.get('https://m3test.2loveyou.com/spin/test/player_read/online_data?limit=30&key=tj_online_1')
    chartData.value = data.result.map((item: TjOnline) => item)
    updateChart() // Call updateChart after fetching new data
  }
  catch (error) {
    console.error('Error fetching profile content:', error)
  }
}

function updateChart() {
  const charEch: ECharts = init(document.getElementById('char') as HTMLElement)
  const option: EChartsOption = {
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
      },
      {
        data: chartData.value.map((item: TjOnline) => item.apple),
        name: 'apple',
        type: 'line',
      },
      {
        data: chartData.value.map((item: TjOnline) => item.jl),
        name: 'jl',
        type: 'line',
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
  }, 60000) // 60000 milliseconds = 1 minute
})
</script>

<template>
  <ShadowCard class="!p-[5px]">
    <div id="char" />
  </ShadowCard>
</template>

<style scoped>
#char {
  z-index: 100;
  width: 100%;
  height: 100%;
}
</style>