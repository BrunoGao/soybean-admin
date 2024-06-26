<script setup lang="ts">
import { onMounted, ref, computed } from 'vue';
import axios from 'axios';
import { $t } from '@/locales';
import { useAppStore } from '@/store/modules/app';
import { useAuthStore } from '@/store/modules/auth';

defineOptions({
  name: 'HeaderBanner'
});

const appStore = useAppStore();
const authStore = useAuthStore();

const gap = computed(() => (appStore.isMobile ? 0 : 16));

interface StatisticData {
  id: number;
  label: string;
  value: string;
}

const statisticData = computed<StatisticData[]>(() => [
  {
    id: 0,
    label: $t('page.home.projectCount'),
    value: '25'
  },
  {
    id: 1,
    label: $t('page.home.todo'),
    value: '4/16'
  },
  {
    id: 2,
    label: $t('page.home.message'),
    value: '12'
  }
]);

const weatherDesc = ref('');

onMounted(async () => {
  try {
    const response = await axios.get(`https://restapi.amap.com/v3/weather/weatherInfo?city=深圳&key=d45f28e481665db5b1145a5aa989e68a`);
    if (response.data && response.data.lives && response.data.lives.length > 0) {
      const weatherInfo = response.data.lives[0];
      weatherDesc.value = `${$t('page.health.weather')}: ${weatherInfo.weather}, ${$t('page.health.temperature')}: ${weatherInfo.temperature}°C, ${$t('page.health.humidity')}: ${weatherInfo.humidity}%`;
    } else {
      weatherDesc.value = $t('page.home.weatherUnavailable');
    }
  } catch (error) {
    console.error('Failed to fetch weather:', error);
    weatherDesc.value = $t('page.home.weatherError');
  }
});
</script>

<template>
  <NCard :bordered="false" class="card-wrapper">
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <NGi span="24 s:24 m:18">
        <div class="flex-y-center">
          <div class="size-72px shrink-0 overflow-hidden rd-1/2">
            <img src="@/assets/imgs/soybean.jpg" class="size-full" />
          </div>
          <div class="pl-12px">
            <h3 class="text-18px font-semibold">
              {{ $t('page.home.greeting', { userName: authStore.userInfo.userName }) }}
            </h3>
            <p class="text-#999 leading-30px">{{ weatherDesc }}</p>
          </div>
        </div>
      </NGi>
      <NGi span="24 s:24 m:6">
        <NSpace :size="24" justify="end">
          <NStatistic v-for="item in statisticData" :key="item.id" class="whitespace-nowrap" v-bind="item" />
        </NSpace>
      </NGi>
    </NGrid>
  </NCard>
</template>

<style scoped></style>
