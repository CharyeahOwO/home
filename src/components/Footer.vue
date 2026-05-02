<template>
  <footer id="footer" :class="store.footerBlur ? 'blur' : null">
    <Transition name="fade" mode="out-in">
      <div v-if="!showMusicFooter" class="power">
        <span>
          <span :class="startYear < fullYear ? 'c-hidden' : 'hidden'">Copyright&nbsp;</span>
          &copy;
          <span v-if="startYear < fullYear"
            class="site-start">
            {{ startYear }}
            -
          </span>
          {{ fullYear }}
          <a :href="siteUrl">{{ siteAuthor }}</a>
        </span>
        <!-- 站点备案 -->
        <span>
          &amp;
          <a v-if="siteIcp" href="https://beian.miit.gov.cn" target="_blank">
            {{ siteIcp }}
          </a>
        </span>
      </div>
      <div
        v-else
        class="lrc"
        role="button"
        tabindex="0"
        @click="openMusicPanel"
        @keydown.enter="openMusicPanel"
      >
        <div class="progress" @click.stop="seekByProgress">
          <div class="progress-bar" :style="{ width: `${store.getPlayerProgress.percent}%` }" />
          <span class="progress-time current">{{ formatTime(store.getPlayerProgress.current) }}</span>
          <span class="progress-time duration">{{ formatTime(store.getPlayerProgress.duration) }}</span>
        </div>
        <div class="quick-controls">
          <button type="button" aria-label="上一首" @click.stop="prevSong">
            <go-start theme="filled" size="18" fill="#efefef" />
          </button>
          <button type="button" class="play" aria-label="播放暂停" @click.stop="toggleMusic">
            <play-one theme="filled" size="22" fill="#efefef" v-show="!store.playerState" />
            <pause theme="filled" size="22" fill="#efefef" v-show="store.playerState" />
          </button>
          <button type="button" aria-label="下一首" @click.stop="nextSong">
            <go-end theme="filled" size="18" fill="#efefef" />
          </button>
        </div>
        <Transition name="fade" mode="out-in">
          <div class="lrc-all" :key="store.getPlayerLrc">
            <music-one theme="filled" size="18" fill="#efefef" />
            <span class="lrc-text text-hidden" v-html="store.getPlayerLrc" />
            <music-one theme="filled" size="18" fill="#efefef" />
          </div>
        </Transition>
      </div>
    </Transition>
  </footer>
</template>

<script setup>
import { GoEnd, GoStart, MusicOne, Pause, PlayOne } from "@icon-park/vue-next";
import { mainStore } from "@/store";

const store = mainStore();
const fullYear = new Date().getFullYear();

// 加载配置数据
// const siteStartDate = ref(import.meta.env.VITE_SITE_START);
const startYear = ref(
  import.meta.env.VITE_SITE_START?.length >= 4 ? 
  import.meta.env.VITE_SITE_START.substring(0, 4) : null
);
const siteIcp = ref(import.meta.env.VITE_SITE_ICP);
const siteAuthor = ref(import.meta.env.VITE_SITE_AUTHOR);
const siteUrl = computed(() => {
  const url = import.meta.env.VITE_SITE_URL;
  if (!url) return "https://mulingowo.cn";
  // 判断协议前缀
  if (!url.startsWith("http://") && !url.startsWith("https://")) {
    return "//" + url;
  }
  return url;
});
const showMusicFooter = computed(() => store.playerLrcShow && !!store.getPlayerData.name);

const formatTime = (seconds) => {
  if (!Number.isFinite(seconds) || seconds <= 0) return "0:00";
  const minute = Math.floor(seconds / 60);
  const second = Math.floor(seconds % 60)
    .toString()
    .padStart(2, "0");
  return `${minute}:${second}`;
};

const seekByProgress = (event) => {
  const duration = store.getPlayerProgress.duration;
  if (!duration) return;
  const rect = event.currentTarget.getBoundingClientRect();
  const percent = Math.min(1, Math.max(0, (event.clientX - rect.left) / rect.width));
  window.dispatchEvent(
    new CustomEvent("player-seek", {
      detail: {
        time: duration * percent,
      },
    }),
  );
};

const openMusicPanel = () => {
  if (!showMusicFooter.value) return;
  if (window.innerWidth > 720) return;
  window.dispatchEvent(new CustomEvent("music-panel-open"));
};

const toggleMusic = () => {
  window.dispatchEvent(new CustomEvent("music-toggle"));
};

const prevSong = () => {
  window.dispatchEvent(new CustomEvent("music-prev"));
};

const nextSong = () => {
  window.dispatchEvent(new CustomEvent("music-next"));
};
</script>

<style lang="scss" scoped>
#footer {
  width: 100%;
  position: absolute;
  bottom: 0;
  left: 0;
  height: 46px;
  line-height: 46px;
  text-align: center;
  z-index: 0;
  font-size: 14px;
  // 文字不换行
  word-break: keep-all;
  white-space: nowrap;
  .power {
    animation: fade 0.3s;
  }
  .lrc {
    padding: 0 20px;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    position: relative;
    height: 100%;
    cursor: pointer;
    .lrc-all {
      width: 98%;
      height: 100%;
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
      .lrc-text {
        margin: 0 8px;
      }
      .i-icon {
        width: 18px;
        height: 18px;
        display: inherit;
      }
    }
    .progress {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 8px;
      cursor: pointer;

      &::before {
        content: "";
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 2px;
        background: rgb(255 255 255 / 18%);
      }

      .progress-bar {
        position: absolute;
        top: 0;
        left: 0;
        height: 2px;
        border-radius: 999px;
        background: linear-gradient(90deg, rgb(255 255 255 / 65%), #fff);
        box-shadow: 0 0 10px rgb(255 255 255 / 28%);
        transition: width 0.2s linear;
      }

      .progress-time {
        position: absolute;
        top: 6px;
        font-size: 10px;
        line-height: 1;
        color: rgb(255 255 255 / 58%);
        opacity: 0;
        transition: opacity 0.2s;
      }

      .current {
        left: 16px;
      }

      .duration {
        right: 16px;
      }

      &:hover {
        &::before,
        .progress-bar {
          height: 3px;
        }

        .progress-time {
          opacity: 1;
        }
      }
    }
    .quick-controls {
      position: absolute;
      right: 18px;
      top: 50%;
      z-index: 2;
      display: flex;
      align-items: center;
      gap: 8px;
      opacity: 0;
      pointer-events: none;
      transform: translateY(calc(-50% + 4px));
      transition:
        opacity 0.2s,
        transform 0.2s;

      button {
        width: 30px;
        height: 30px;
        padding: 0;
        border: 1px solid rgb(255 255 255 / 10%);
        border-radius: 50%;
        background: rgb(0 0 0 / 22%);
        backdrop-filter: blur(10px);
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        transition:
          transform 0.2s,
          background 0.2s;

        &.play {
          width: 36px;
          height: 36px;
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
    }

    &:hover {
      .quick-controls {
        opacity: 1;
        pointer-events: auto;
        transform: translateY(-50%);
      }
    }
  }
  &.blur {
    backdrop-filter: blur(10px);
    background: rgb(0 0 0 / 25%);
    font-size: 16px;
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.15s ease-in-out;
  }
  @media (max-width: 720px) {
    font-size: 0.9rem;
    &.blur {
      font-size: 0.9rem;
    }
    .lrc {
      padding: 0 14px;

      .quick-controls {
        display: none;
      }

      .progress {
        height: 7px;

        .progress-time {
          display: none;
        }
      }
    }
  }
  @media (max-width: 560px) {
    .c-hidden {
      display: none;
    }
  }
  @media (max-width: 480px) {
    .hidden {
      display: none;
    }
  }
}
</style>
