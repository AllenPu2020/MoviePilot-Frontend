<script lang="ts" setup>
import api from "@/api";
import type { DownloadingInfo } from "@/api/types";
import NoDataFound from "@/components/NoDataFound.vue";
import DownloadingCard from "@/components/cards/DownloadingCard.vue";
import PullRefresh from "pull-refresh-vue3";

// 定时器
let refreshTimer: NodeJS.Timer | null = null;

// 数据列表
const dataList = ref<DownloadingInfo[]>([]);

// 获取订阅列表数据
const fetchData = async () => {
  try {
    dataList.value = await api.get("download");
    isRefreshed.value = true;
  } catch (error) {
    console.error(error);
  }
};

// 刷新状态
const loading = ref(false);

// 是否刷新过
const isRefreshed = ref(false);

// 下拉刷新
const onRefresh = () => {
  loading.value = true;
  fetchData();
  loading.value = false;
};

// 加载时获取数据
onBeforeMount(() => {
  fetchData();
  // 启动定时器
  refreshTimer = setInterval(() => {
    fetchData();
  }, 3000);
});

// 组件卸载时停止定时器
onUnmounted(() => {
  if (refreshTimer) {
    clearInterval(refreshTimer);
    refreshTimer = null;
  }
});
</script>

<template>
  <VProgressCircular
    class="centered"
    v-if="!isRefreshed"
    indeterminate
    color="primary"
  ></VProgressCircular>
  <PullRefresh v-model="loading" @refresh="onRefresh">
    <div class="grid gap-3 grid-downloading-card" v-if="dataList.length > 0">
      <DownloadingCard v-for="data in dataList" :key="data.hash" :info="data" />
    </div>
    <NoDataFound
      v-if="dataList.length === 0 && isRefreshed"
      error-code="404"
      error-title="没有任务"
      error-description="正在下载的任务将会显示在这里。"
    >
    </NoDataFound>
  </PullRefresh>
</template>

<style type="scss">
.grid-downloading-card {
  grid-template-columns: repeat(auto-fill, minmax(20rem, 1fr));
  padding-block-end: 1rem;
}

</style>
