<template>
  <!-- 功能区域 -->
  <div :class="store.mobileFuncState ? 'function mobile' : 'function'">
    <Music v-if="playerHasId" />
    <div class="time-card cards">
      <div class="date">
        <span>{{ currentTime.year }}&nbsp;年&nbsp;</span>
        <span>{{ currentTime.month }}&nbsp;月&nbsp;</span>
        <span>{{ currentTime.day }}&nbsp;日&nbsp;</span>
        <span class="sm-hidden">{{ currentTime.weekday }}</span>
      </div>
      <div class="text">
        <span>{{ currentTime.hour }}:{{ currentTime.minute }}:{{ currentTime.second }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { getCurrentTime } from "@/utils/getTime";
import { mainStore } from "@/store";
import Music from "@/components/Music.vue";

const store = mainStore();

// 当前时间
const currentTime = ref({});
const timeInterval = ref(null);

// 播放器 id
const playerHasId = import.meta.env.VITE_SONG_ID;

// 更新时间
const updateTimeData = () => {
  currentTime.value = getCurrentTime();
};

onMounted(() => {
  updateTimeData();
  timeInterval.value = setInterval(updateTimeData, 1000);
});

onBeforeUnmount(() => {
  clearInterval(timeInterval.value);
});
</script>

<style lang="scss" scoped>
.function {
  .time-card {
    position: fixed;
    top: 96px;
    right: 96px;
    width: 250px;
    padding: 16px 20px;
    text-align: center;
    animation: fade 0.5s;
    z-index: 1;

    .date {
      font-size: 0.95rem;
      text-overflow: ellipsis;
      overflow-x: hidden;
      white-space: nowrap;
    }

    .text {
      margin-top: 8px;
      font-size: 2.65rem;
      letter-spacing: 0;
      line-height: 1;
      font-family: "mao", "Microsoft YaHei", sans-serif;
    }

    @media (max-width: 1200px) {
      top: 84px;
      right: 48px;
    }

    @media (max-width: 720px) {
      display: none;
    }
  }
}
</style>
