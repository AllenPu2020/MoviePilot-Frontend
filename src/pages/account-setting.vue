<script lang="ts" setup>
import AccountSettingAccount from "@/views/account-setting/AccountSettingAccount.vue";
import AccountSettingSystem from "@/views/account-setting/AccountSettingSystem.vue";
import { useRoute } from "vue-router";

const route = useRoute();

const activeTab = ref(route.params.tab);

// tabs
const tabs = [
  { title: "用户", icon: "mdi-account-outline", tab: "account" },
  { title: "进阶", icon: "mdi-monitor-dashboard", tab: "system" },
];
</script>

<template>
  <div>
    <VTabs v-model="activeTab" show-arrows>
      <VTab v-for="item in tabs" :key="item.icon" :value="item.tab">
        <VIcon size="20" start :icon="item.icon" />
        {{ item.title }}
      </VTab>
    </VTabs>
    <VDivider />

    <VWindow v-model="activeTab" class="mt-5 disable-tab-transition">
      <!-- Account -->
      <VWindowItem value="account">
        <AccountSettingAccount />
      </VWindowItem>

      <!-- System -->
      <VWindowItem value="system">
        <AccountSettingSystem />
      </VWindowItem>
    </VWindow>
  </div>
</template>
