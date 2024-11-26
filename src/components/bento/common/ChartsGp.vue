<script setup lang="ts">
import { onMounted } from 'vue'
// Echarts 为init（dom元素后的类型）
// EChartsOption 为 option 的类型
import type { ECharts, EChartsOption } from 'echarts'
import { init } from 'echarts'
import axios from 'axios'

interface TjOnline {
  day: string
  ma_price5: number
}

const chartData = ref([])

const cc = ref(400)
const cb = ref(1.533)

async function fetchContent() {
  try {
    const { data } = await axios.get(`https://api.bameiapp.com/spin/test/player_read/online_data_gp`)

    chartData.value = data.map((item: TjOnline) => ({
      day: item.day,
      ma_price5: Number.parseFloat(((item.ma_price5 - cb.value) * cc.value).toFixed(3)),
    }))
    updateChart() // Call updateChart after fetching new data
  }
  catch (error) {
    console.error('Error fetching profile content:', error)
  }
}

function updateChart() {
  const charEch: ECharts = init(document.getElementById('chart_gp') as HTMLElement, 'dark')
  const option: EChartsOption = {
    darkMode: true,
    title: {
      text: 'RMB',
    },
    tooltip: {
      trigger: 'axis',
    },
    xAxis: {
      type: 'category',
      data: chartData.value.map((item: TjOnline) => item.day),
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        name: '亏损',
        data: chartData.value.map((item: TjOnline) =>
          item.ma_price5 <= 0 ? item.ma_price5 : null,
        ),
        type: 'line',
        smooth: true,
        lineStyle: {
          color: 'green', // Color for data points below 1.55
        },
      },
      {
        name: '盈利',
        data: chartData.value.map((item: TjOnline) =>
          item.ma_price5 > 0 ? item.ma_price5 : null,
        ),
        type: 'line',
        smooth: true,
        lineStyle: {
          color: 'red', // Color for data points above 1.55
        },
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
    <div id="chart_gp" />
  </ShadowCard>
</template>

<style scoped>
#chart_gp {
  z-index: 100;
  width: 100%;
  height: 100%;
}
</style>
