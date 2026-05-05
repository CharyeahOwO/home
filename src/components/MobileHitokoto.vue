<template>
  <div class="mobile-hitokoto cards" @click="updateHitokoto">
    <span class="text">{{ hitokotoData.text }}</span>
    <span class="from">-「&nbsp;{{ hitokotoData.from }}&nbsp;」</span>
  </div>
</template>

<script setup>
import { getHitokoto } from "@/api";
import debounce from "@/utils/debounce.js";

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

const updateHitokoto = () => {
  debounce(() => {
    getHitokotoData();
  }, 500);
};

onMounted(() => {
  getHitokotoData();
});
</script>

<style lang="scss" scoped>
.mobile-hitokoto {
  display: none;

  @media (max-width: 720px) {
    width: min(90%, 360px);
    margin: 0 auto 1rem;
    padding: 0.8rem 1rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.35rem;
    text-align: center;
    line-height: 1.55;
    animation: fade 0.5s;

    .text {
      font-size: 0.9rem;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
    }

    .from {
      font-size: 0.86rem;
      font-weight: 700;
      opacity: 0.9;
    }
  }
}
</style>
