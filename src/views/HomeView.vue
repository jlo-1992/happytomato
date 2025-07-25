<template>
  <v-container class="container">
    <v-row>
      <v-col cols="2" />
      <v-col cols="6" style="height: 200px">
        <h1>TOMATO IN ACTION</h1>
        <h2 class="text-center">{{ list.currentItem }}</h2>
        <!-- <h2>{{ list.timeleft }}</h2>  -->
        <!-- <h2>{{ timeLeftText }}</h2> -->
        <img
          alt="fakey happy tomato"
          class="tomato"
          :class="{ bouncing: status === STATUS.COUNTING }"
          src="../assets/tomato.png"
        />
      </v-col>
      <v-col class="col" cols="12">
        <DigitNumber v-for="(data, i) in timeLeftText" :key="i" color="white" :data="data" />
      </v-col>
      <v-col class="col" cols="12">
        <!--
          開始按鈕的停用條件:
          1. 倒數中
          2. 目前進行事項或未完成事項沒有項目
        -->
        <v-btn
          :disabled="
            status === STATUS.COUNTING || (list.currentItem.length === 0 && list.items.length === 0)
          "
          icon="mdi-play"
          @click="startTimer"
        />
        <!-- 只有倒數中才能按暫停 -->
        <v-btn :disabled="status !== STATUS.COUNTING" icon="mdi-pause" @click="pause" />
        <!-- 目前有事項才能跳過 -->
        <v-btn :disabled="list.currentItem.length === 0" icon="mdi-skip-next" @click="finish" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useWebNotification } from '@vueuse/core'
import { computed, ref } from 'vue'
import DigitNumber from '@/components/DigitNumber.vue'
import { useListStore } from '@/stores/list'
import { useSettingsStore } from '@/stores/settings'

const list = useListStore()
const settings = useSettingsStore()
// show 顯示，isSupported 判斷瀏覽器有沒有支援

// 倒數狀態
const STATUS = {
  STOP: 0,
  COUNTING: 1,
  PAUSE: 2,
}
const status = ref(STATUS.STOP)

// 計時器
let timer = 0
// 開始計時器
// 暫停繼續 + 停止開始
const startTimer = () => {
  // 如果是停止開始，更新目前事項
  if (status.value === STATUS.STOP && list.items.length > 0) {
    list.setCurrentItem()
  }

  // 狀態變成倒數中
  status.value = STATUS.COUNTING

  // 開始倒數
  timer = setInterval(() => {
    list.countdown()

    if (list.timeleft < 0) {
      finish(timer)
    }
  }, 1000)
}

const pause = () => {
  clearInterval(timer)
  status.value = STATUS.PAUSE
}

const finish = () => {
  // 停止計時器
  clearInterval(timer)

  // 更改狀態為停止
  status.value = STATUS.STOP

  // 播放設定的音樂
  const audio = new Audio()
  audio.src = settings.selectedAlarm.file
  audio.play()

  // 要先在終端機輸入：npm i @vueuse/core，安裝 vueuse，才能使用
  // 設定彈出視窗通知已完成，https://vueuse.org/core/useWebNotification/#usewebnotification`
  const { show, isSupported } = useWebNotification({
    title: '事項完成',
    body: list.currentItem,
    icon: new URL('@/assets/tomato.png', import.meta.url).href,
  })

  if (isSupported.value) {
    show()
  }

  // 把完成的目前進行事項放到已完成事項的陣列內
  list.setFinishItem()

  if (list.items.length > 0) {
    startTimer()
  }
}

const timeLeftText = computed(() => {
  // Math.floor 無條件捨去，Math.ceil 無條件進位
  const m = Math.floor(list.timeleft / 60)
    .toString()
    // padStart(設定顯示幾位數, 補的數字)
    .padStart(2, '0')
  const s = (list.timeleft % 60).toString().padStart(2, '0')
  return m + ':' + s
})
</script>

<style scoped lang="scss">
$bounce-height: 120px;
$bounce-duration: 3s;

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  backface-visibility: hidden;
  position: relative;

  .col {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    height: 350px;
  }
  .tomato {
    position: absolute;
    top: 15%;
    left: 33%;
    // left:60%;
    width: 130px;

    &.bouncing {
      animation: bounce 6s cubic-bezier(0.42, 0, 0.58, 1) infinite;
      transform-origin: bottom center;
    }
  }
}

@keyframes bounce {
  0% {
    transform: translate(0px, 20%) scale(1);
  }
  5% {
    transform: translate(50px, -40px) scale(1.05);
  }
  10% {
    transform: translate(100px, 20%) scale(0.95);
  }
  15% {
    transform: translate(150px, -40px) scale(1.05);
  }
  20% {
    transform: translate(200px, 20%) scale(0.95);
  }
  25% {
    transform: translate(250px, -40px) scale(1.05);
  }
  30% {
    transform: translate(300px, 20%) scale(0.95);
  }
  35% {
    transform: translate(350px, -40px) scale(1.05);
  }
  40% {
    transform: translate(400px, 20%) scale(0.95);
  }
  45% {
    transform: translate(450px, -40px) scale(1.05);
  }
  50% {
    transform: translate(500px, 20%) scale(0.95);
  }

  55% {
    transform: translate(450px, -40px) scale(1.05);
  }
  60% {
    transform: translate(400px, 20%) scale(0.95);
  }
  65% {
    transform: translate(350px, -40px) scale(1.05);
  }
  70% {
    transform: translate(300px, 20%) scale(0.95);
  }
  75% {
    transform: translate(250px, -40px) scale(1.05);
  }
  80% {
    transform: translate(200px, 20%) scale(0.95);
  }
  85% {
    transform: translate(150px, -40px) scale(1.05);
  }
  90% {
    transform: translate(100px, 20%) scale(0.95);
  }
  95% {
    transform: translate(50px, -40px) scale(1.05);
  }
  100% {
    transform: translate(0px, 20%) scale(0.95);
  }
}
</style>
