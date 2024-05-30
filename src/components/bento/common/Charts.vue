<script setup lang="ts">
import { onMounted } from 'vue'
// Echarts 为init（dom元素后的类型）
// EChartsOption 为 option 的类型
import type { ECharts, EChartsOption } from 'echarts'
import { init } from 'echarts'
import axios from 'axios'

const chartData = ref([])

async function fetchContent() {
  try {
    const { data } = await axios.get('https://m3test.2loveyou.com/spin/test/player_read/online_data?limit=25')
    chartData.value = data.result.map((item: any) => ({
      date: item.date,
      onlineNum: item.onlineNum,
    }))
  }
  catch (error) {
    console.error('Error fetching profile content:', error)
  }
}

onMounted(async () => {
  await fetchContent()
  const charEle = document.getElementById('char') as HTMLElement
  const charEch: ECharts = init(charEle)
  const option: EChartsOption = {
    xAxis: {
      type: 'category',
      data: chartData.value.map(item => item.date),
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        data: chartData.value.map(item => item.onlineNum),
        type: 'line',
      },
    ],
  }
  charEch.setOption(option)
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
