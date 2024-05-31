<script setup lang="ts">
const time = ref(countdownTime()) // 23.84

function runAtMidnight(callback: Function) {
  setInterval(() => {
    callback()
  }, 1000 * 30)
}

runAtMidnight(() => {
  time.value = countdownTime()
})

function countdownTime() {
  // 获取当前时间的日期对象
  const today = new Date()

  // 设置今天的8点半
  const eightThirtyAM = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 8, 30, 0)

  // 设置今天下午5点半
  const fiveThirtyPM = new Date(today.getFullYear(), today.getMonth(), today.getDate(), 17, 30, 0)

  // 获取总时间
  const total = fiveThirtyPM.getTime() - eightThirtyAM.getTime()

  // 获取已经过去的时间
  let already = today.getTime() - eightThirtyAM.getTime()

  if (already < 0)
    already = 0

  // 计算已经过去的百分比
  const percentage = ((already / total) * 100).toFixed(2)
  return Number(percentage) < 10 ? `0${percentage}` : percentage
}
</script>

<template>
  <ShadowBlock class="h-[300px] w-[400px] text-[5rem]">
    <div class="relative h-full w-full" style="font-family: Digital">
      <span class="absolute left-[50%] top-[50%] w-[180px] flex flex-row -translate-x-1/2 -translate-y-1/2">
        <span>{{ time.slice(0, 2) }}</span>
        <div class="relative grid w-fit place-items-center">
          <div class="opacity-0">:</div>
          <div class="absolute left-[1px] top-0 w-full">.</div>
        </div>
        <span>{{ time.slice(3) }}</span>
      </span>
      <span
        v-show="Number(time) !== 100 && Number(time) !== 100.00"
        class="absolute left-[50%] top-[50%] w-[180px] text-[#0000001c] -translate-x-1/2 -translate-y-1/2 dark:text-[#ffffff1c]"
      >
        00:00
      </span>

      <span class="absolute left-[calc(50%_-_150px)] top-[50%] text-[24px] opacity-30">
        <span>HOME</span>
      </span>
      <span class="absolute left-[calc(50%_+_100px)] top-[50%] text-[24px] opacity-30">
        %
      </span>
    </div>
  </ShadowBlock>
</template>

<style scoped>

</style>
