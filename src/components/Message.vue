<template>
  <!-- 基本信息 -->
  <div class="message">
    <!-- Logo -->
    <div class="logo">
      <div class="name text-hidden">
        <span class="bg">{{ siteName }}</span>
        <span class="sub">{{ siteSubName }}</span>
      </div>
    </div>
    <!-- 简介 -->
    <div class="description cards" @click="changeBox">
      <div class="content">
        <Icon size="16">
          <QuoteLeft />
        </Icon>
        <Transition name="fade" mode="out-in">
          <div :key="descriptionText.hello + descriptionText.text" class="text">
            <p>{{ descriptionText.hello }}</p>
            <p>{{ descriptionText.text }}</p>
            <div class="hitokoto-line">
              <span>{{ hitokotoData.text }}</span>
              <span class="from">-「&nbsp;{{ hitokotoData.from }}&nbsp;」</span>
            </div>
          </div>
        </Transition>
        <Icon size="16">
          <QuoteRight />
        </Icon>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from "@vicons/utils";
import { QuoteLeft, QuoteRight } from "@vicons/fa";
import { Error } from "@icon-park/vue-next";
import { getHitokoto } from "@/api";
import { mainStore } from "@/store";
const store = mainStore();

// 主页显示名称
const siteName =
  import.meta.env.VITE_SITE_DISPLAY_NAME || import.meta.env.VITE_SITE_NAME || "Sakura Realm";
const siteSubName = import.meta.env.VITE_SITE_NAME || "樱落之境";

// 简介区域文字
const descriptionText = reactive({
  hello: import.meta.env.VITE_DESC_HELLO,
  text: import.meta.env.VITE_DESC_TEXT,
});

const hitokotoData = reactive({
  text: "星光落在未写完的故事里。",
  from: "樱落之境",
});

const getHitokotoData = async () => {
  try {
    const result = await getHitokoto();
    hitokotoData.text = result.hitokoto;
    hitokotoData.from = result.from;
  } catch (error) {
    console.error("一言获取失败", error);
  }
};

// 切换右侧功能区
const changeBox = () => {
  if (store.getInnerWidth >= 721) {
    store.boxOpenState = !store.boxOpenState;
  } else {
    ElMessage({
      message: "当前页面宽度不足以开启盒子",
      grouping: true,
      icon: h(Error, {
        theme: "filled",
        fill: "#efefef",
      }),
    });
  }
};

// 监听状态变化
watch(
  () => store.boxOpenState,
  (value) => {
    if (value) {
      descriptionText.hello = import.meta.env.VITE_DESC_HELLO_OTHER;
      descriptionText.text = import.meta.env.VITE_DESC_TEXT_OTHER;
    } else {
      descriptionText.hello = import.meta.env.VITE_DESC_HELLO;
      descriptionText.text = import.meta.env.VITE_DESC_TEXT;
    }
  },
);

onMounted(() => {
  getHitokotoData();
});
</script>

<style lang="scss" scoped>
.message {
  .logo {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    animation: fade 0.5s;
    max-width: 460px;

    .name {
      width: auto;
      overflow: visible;
      padding-left: 0;
      transform: none;
      display: flex;
      flex-direction: column;
      align-items: center;
      font-family: "title-script", "mao", "Microsoft YaHei", sans-serif;
      font-weight: 400;
      letter-spacing: 0;
      text-shadow: 0 4px 24px rgb(255 190 210 / 45%);

      .bg {
        font-size: 4.85rem;
        line-height: 1.12;
        padding: 0 0.32em 0.04em;
        overflow: visible;
      }

      .sub {
        margin-top: 0.15rem;
        font-family: "mao", "Microsoft YaHei", sans-serif;
        font-size: 1rem;
        opacity: 0.82;
        text-shadow: 0 2px 12px rgb(0 0 0 / 35%);
      }
    }
    @media (max-width: 768px) {
      justify-content: center;

      .name {
        width: auto;
        height: 128px;
        padding-left: 0;
        display: flex;
        align-items: center;
        transform: none;

        .bg {
          font-size: 3.85rem;
          padding: 0 0.32em 0.04em;
        }

        .sub {
          font-size: 0.95rem;
        }
      }
    }

    @media (max-width: 720px) {
      max-width: 100%;
    }
  }

  .description {
    padding: 1rem;
    margin-top: 3.5rem;
    max-width: 460px;
    animation: fade 0.5s;

    .content {
      display: flex;
      justify-content: space-between;

      .text {
        margin: 0.75rem 1rem;
        line-height: 2rem;
        margin-right: auto;
        transition: opacity 0.2s;

        p {
          &:nth-of-type(1) {
            font-family: "title-script", "mao", "Microsoft YaHei", sans-serif;
            font-size: 1.35rem;
            font-weight: 400;
          }
        }

        .hitokoto-line {
          margin-top: 0.75rem;
          padding-top: 0.75rem;
          border-top: 1px solid rgb(255 255 255 / 18%);
          display: flex;
          flex-direction: column;
          line-height: 1.65;
          opacity: 0.92;

          .from {
            align-self: flex-end;
            margin-top: 0.25rem;
            font-weight: 700;
          }
        }
      }

      .xicon:nth-of-type(2) {
        align-self: flex-end;
      }
    }
    @media (max-width: 720px) {
      width: min(94%, 360px);
      max-width: 520px;
      margin-left: auto;
      margin-right: auto;
      padding: 1rem 1.15rem;
      pointer-events: none;

      .content {
        .text {
          width: 100%;
          margin: 0.65rem 0.5rem;
          line-height: 1.85rem;
          text-align: center;

          .hitokoto-line {
            display: none;
            align-items: center;

            .from {
              align-self: center;
            }
          }
        }
      }
    }

    @media (max-width: 390px) {
      width: 94%;
      padding: 0.9rem 0.95rem;

      .content {
        .text {
          margin-left: 0.35rem;
          margin-right: 0.35rem;
          line-height: 1.75rem;
          font-size: 0.95rem;

          p {
            &:nth-of-type(1) {
              font-size: 1.2rem;
            }
          }
        }
      }
    }
  }
  // @media (max-width: 390px) {
  //   .logo {
  //     flex-direction: column;
  //     .logo-img {
  //       display: none;
  //     }
  //     .name {
  //       margin-left: 0;
  //       height: auto;
  //       transform: none;
  //       text-align: center;
  //       .bg {
  //         font-size: 3.5rem;
  //       }
  //       .sm {
  //         font-size: 1.4rem;
  //       }
  //     }
  //   }
  //   .description {
  //     margin-top: 2.5rem;
  //   }
  // }
}
</style>
