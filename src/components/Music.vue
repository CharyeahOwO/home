<template>
  <!-- 音乐控制面板 -->
  <div
    class="music"
    @mouseenter="volumeShow = true"
    @mouseleave="volumeShow = false"
    v-show="store.musicOpenState"
  >
    <div class="btns">
      <span @click="openMusicList()">音乐列表</span>
      <span @click="store.musicOpenState = false">回到一言</span>
    </div>
    <div class="control">
      <go-start theme="filled" size="30" fill="#efefef" @click="changeMusicIndex(0)" />
      <Transition name="fade" mode="out-in">
        <div :key="store.playerState" class="state" @click="changePlayState">
          <play-one theme="filled" size="50" fill="#efefef" v-show="!store.playerState" />
          <pause theme="filled" size="50" fill="#efefef" v-show="store.playerState" />
        </div>
      </Transition>
      <go-end theme="filled" size="30" fill="#efefef" @click="changeMusicIndex(1)" />
    </div>
    <div class="menu">
      <div class="name" v-show="!volumeShow">
        <span>{{
          store.getPlayerData.name
            ? store.getPlayerData.name + " - " + store.getPlayerData.artist
            : "未播放音乐"
        }}</span>
      </div>
      <div class="volume" v-show="volumeShow">
        <div class="icon">
          <volume-mute theme="filled" size="24" fill="#efefef" v-if="volumeNum == 0" />
          <volume-small
            theme="filled"
            size="24"
            fill="#efefef"
            v-else-if="volumeNum > 0 && volumeNum < 0.7"
          />
          <volume-notice theme="filled" size="24" fill="#efefef" v-else />
        </div>
        <el-slider v-model="volumeNum" :show-tooltip="false" :min="0" :max="1" :step="0.01" />
      </div>
    </div>
  </div>
  <!-- 音乐列表弹窗 -->
  <Transition name="fade" mode="out-in">
    <div class="music-list" v-show="musicListShow" @click="closeMusicList()">
      <Transition name="zoom">
        <div class="list" v-show="musicListShow" @click.stop>
          <close-one
            class="close"
            theme="filled"
            size="28"
            fill="#ffffff60"
            @click="closeMusicList()"
          />
          <div class="panel-controls">
            <div class="song-info">
              <span class="song-name">{{ store.getPlayerData.name || "未播放音乐" }}</span>
              <span class="song-artist" v-if="store.getPlayerData.artist">
                {{ store.getPlayerData.artist }}
              </span>
            </div>
            <div class="panel-actions">
              <button type="button" class="panel-action" aria-label="上一首" @click="changeMusicIndex(0)">
                <go-start theme="filled" size="24" fill="#efefef" />
              </button>
              <button type="button" class="panel-action play" aria-label="播放暂停" @click="changePlayState">
                <play-one theme="filled" size="34" fill="#efefef" v-show="!store.playerState" />
                <pause theme="filled" size="34" fill="#efefef" v-show="store.playerState" />
              </button>
              <button type="button" class="panel-action" aria-label="下一首" @click="changeMusicIndex(1)">
                <go-end theme="filled" size="24" fill="#efefef" />
              </button>
            </div>
          </div>
          <Player
            ref="playerRef"
            :songServer="playerData.server"
            :songType="playerData.type"
            :songId="playerData.id"
            :volume="volumeNum"
            :listMaxHeight="listMaxHeight"
          />
        </div>
      </Transition>
    </div>
  </Transition>
</template>

<script setup>
import {
  GoStart,
  PlayOne,
  Pause,
  GoEnd,
  CloseOne,
  VolumeMute,
  VolumeSmall,
  VolumeNotice,
} from "@icon-park/vue-next";
import Player from "@/components/Player.vue";
import { mainStore } from "@/store";
const store = mainStore();

// 音量条数据
const volumeShow = ref(false);
const volumeNum = ref(store.musicVolume ? store.musicVolume : 0.7);

// 播放列表数据
const musicListShow = ref(false);
const playerRef = ref(null);
const playerData = reactive({
  server: import.meta.env.VITE_SONG_SERVER,
  type: import.meta.env.VITE_SONG_TYPE,
  id: import.meta.env.VITE_SONG_ID,
});
const listMaxHeight = computed(() => (store.innerWidth <= 720 ? 300 : 360));

// 开启播放列表
const openMusicList = () => {
  musicListShow.value = true;
  nextTick(() => {
    playerRef.value?.showList();
  });
};

// 关闭播放列表
const closeMusicList = () => {
  musicListShow.value = false;
};

// 音乐播放暂停
const changePlayState = () => {
  playerRef.value?.playToggle();
};

// 音乐上下曲
const changeMusicIndex = (type) => {
  playerRef.value?.changeSong(type);
};

const handleMusicToggle = () => {
  changePlayState();
};

const handleMusicPrev = () => {
  changeMusicIndex(0);
};

const handleMusicNext = () => {
  changeMusicIndex(1);
};

const handleKeydown = (e) => {
  if (!store.musicIsOk) {
    return;
  }
  if (e.code == "Space") {
    changePlayState();
  }
};

onMounted(() => {
  // 空格键事件
  window.addEventListener("keydown", handleKeydown);
  window.addEventListener("music-panel-open", openMusicList);
  window.addEventListener("music-toggle", handleMusicToggle);
  window.addEventListener("music-prev", handleMusicPrev);
  window.addEventListener("music-next", handleMusicNext);
  // 挂载方法至 window
  window.$openList = openMusicList;
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleKeydown);
  window.removeEventListener("music-panel-open", openMusicList);
  window.removeEventListener("music-toggle", handleMusicToggle);
  window.removeEventListener("music-prev", handleMusicPrev);
  window.removeEventListener("music-next", handleMusicNext);
});

// 监听音量变化
watch(
  () => volumeNum.value,
  (value) => {
    store.musicVolume = value;
    playerRef.value?.changeVolume(store.musicVolume);
  },
);
</script>

<style lang="scss" scoped>
.music {
  width: 100%;
  height: 100%;
  background: #00000026;
  backdrop-filter: blur(10px);
  border-radius: 6px;
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-direction: column;
  animation: fade 0.5s;
  .btns {
    display: flex;
    align-items: center;
    margin-bottom: 6px;
    span {
      background: #ffffff26;
      padding: 2px 8px;
      border-radius: 6px;
      margin: 0px 6px;
      text-overflow: ellipsis;
      overflow-x: hidden;
      white-space: nowrap;
      &:hover {
        background: #ffffff4d;
      }
    }
  }
  .control {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-evenly;
    width: 100%;
    .state {
      transition: opacity 0.1s;
      .i-icon {
        width: 50px;
        height: 50px;
        display: block;
      }
    }
    .i-icon {
      width: 36px;
      height: 36px;
      display: flex;
      border-radius: 6px;
      align-items: center;
      justify-content: center;
      border-radius: 6px;
      transform: scale(1);
      &:hover {
        background: #ffffff33;
      }
      &:active {
        transform: scale(0.95);
      }
    }
  }
  .menu {
    height: 26px;
    width: 100%;
    line-height: 26px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    .name {
      width: 100%;
      text-align: center;
      text-overflow: ellipsis;
      overflow-x: hidden;
      white-space: nowrap;
      animation: fade 0.3s;
    }
    .volume {
      width: 100%;
      padding: 0 12px;
      display: flex;
      align-items: center;
      flex-direction: row;
      animation: fade 0.3s;
      .icon {
        margin-right: 12px;
        span {
          width: 24px;
          height: 24px;
          display: block;
        }
      }
      :deep(*) {
        transition: none;
      }
      :deep(.el-slider__button) {
        transition: 0.3s;
      }
      .el-slider {
        margin-right: 12px;
        --el-slider-main-bg-color: #efefef;
        --el-slider-runway-bg-color: #ffffff40;
        --el-slider-button-size: 16px;
      }
    }
  }
}
.music-list {
  position: fixed;
  top: 0;
  left: 0;
  margin: auto;
  width: 100%;
  height: 100%;
  background-color: #00000066;
  backdrop-filter: blur(20px);
  z-index: 1;
  .list {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    top: calc(50% - 300px);
    left: calc(50% - 320px);
    width: 640px;
    height: 600px;
    background-color: #ffffff4d;
    border-radius: 6px;
    padding: 42px 36px 30px;
    z-index: 999;
    @media (max-width: 720px) {
      left: calc(50% - 45%);
      width: 90%;
      height: min(620px, 82vh);
      top: 9vh;
      padding: 42px 18px 24px;
    }
    .close {
      position: absolute;
      top: 12px;
      right: 12px;
      width: 28px;
      height: 28px;
      display: block;
      &:hover {
        transform: scale(1.2);
      }
      &:active {
        transform: scale(0.95);
      }
    }
    .panel-controls {
      width: 80%;
      margin-bottom: 14px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 20px;

      .song-info {
        min-width: 0;
        display: flex;
        flex-direction: column;
        line-height: 1.35;

        .song-name,
        .song-artist {
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
        }

        .song-name {
          font-size: 1rem;
        }

        .song-artist {
          margin-top: 2px;
          color: rgb(255 255 255 / 72%);
          font-size: 0.82rem;
        }
      }

      .panel-actions {
        flex: 0 0 auto;
        display: flex;
        align-items: center;
        gap: 10px;
      }

      .panel-action {
        width: 38px;
        height: 38px;
        padding: 0;
        border: 1px solid rgb(255 255 255 / 10%);
        border-radius: 50%;
        background: rgb(0 0 0 / 16%);
        color: #fff;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        transition:
          transform 0.2s,
          background 0.2s;

        &.play {
          width: 48px;
          height: 48px;
          background: rgb(255 255 255 / 18%);
        }

        &:hover {
          background: rgb(255 255 255 / 24%);
          transform: translateY(-1px);
        }

        &:active {
          transform: scale(0.95);
        }

        .i-icon {
          display: flex;
        }
      }

      @media (max-width: 720px) {
        width: 92%;
        flex-direction: column;
        gap: 12px;
        margin-bottom: 12px;
        text-align: center;

        .song-info {
          width: 100%;
        }
      }
    }
  }
}

// 弹窗动画
.zoom-enter-active {
  animation: zoom 0.4s ease-in-out;
}
.zoom-leave-active {
  animation: zoom 0.3s ease-in-out reverse;
}
@keyframes zoom {
  0% {
    opacity: 0;
    transform: scale(0) translateY(-600px);
  }
  100% {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}
</style>
