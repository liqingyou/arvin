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
  currentCont: number
}

const chartData = ref([])
const limitData = ref(100)

async function fetchContent() {
  try {
    const { data } = await axios.get(`https://m3test.2loveyou.com/spin/test/player_read/online_data?limit=${limitData.value}`)
    chartData.value = data.result.reverse().map((item: TjOnline) => ({
      date: item.date,
      onlineNum: item.onlineNum,
      currentCont: item.currentCont,
    }))
    updateChart() // Call updateChart after fetching new data
  }
  catch (error) {
    console.error('Error fetching profile content:', error)
  }
}

function updateChart() {
  const charEch: ECharts = init(document.getElementById('char') as HTMLElement, 'dark')
  const option: EChartsOption = {
    darkMode: true,
    title: {
      text: 'Online Line',
    },
    tooltip: {
      trigger: 'axis',
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
        name: '在线人数',
        data: chartData.value.map((item: TjOnline) => item.onlineNum),
        type: 'line',
        smooth: true,
      },
      {
        name: '连接数',
        data: chartData.value.map((item: TjOnline) => item.currentCont ? item.currentCont : 0),
        type: 'line',
        smooth: true,
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
    <!--    <div> -->
    <!--      数量: -->
    <!--      <input v-model="limitData" type="number"> -->
    <!--    </div> -->
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
