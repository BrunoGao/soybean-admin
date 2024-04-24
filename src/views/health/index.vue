<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import { useAppStore } from '@/store/modules/app';
import HeaderBanner from './modules/header-banner.vue';
import CardData from './modules/card-data.vue';
import HeartRateChart from './modules/heart-rate.vue';
import TemperatureChart from './modules/temperature.vue';
import BloodOxygenChart from './modules/blood-oxygen.vue';
import StepChart from './modules/step.vue';
import PressureHighChart from './modules/pressure-high.vue';
import PressureLowChart from './modules/pressure-low.vue';
import GaugeChart from './modules/gauge.vue';

const appStore = useAppStore();
const gap = computed(() => (appStore.isMobile ? 0 : 16));

// 定义响应式引用来存储每个字段的数据和时间戳
const healthData = ref({
  bloodOxygen: [],
  heartrate: [],
  pressureHigh: [],
  pressureLow: [],
  step: [],
  temperature: [],
  timestamps: []
});

async function fetchHealthData() {
  try {
    const response = await fetch(
      'http://127.0.0.1:5000/fetch_health_data?phone_number=18926066506&timestamp=2024-04-23'
    );
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const responseData = await response.json();

    // Check if responseData.data exists and is an array
    if (responseData.success && Array.isArray(responseData.data)) {
      healthData.value = responseData.data.reduce(
        (acc, data) => {
          acc.bloodOxygen.push(data.bloodOxygen);
          acc.heartrate.push(data.heartrate);
          acc.pressureHigh.push(data.pressureHigh);
          acc.pressureLow.push(data.pressureLow);
          acc.step.push(data.step);
          acc.temperature.push(data.temperature);
          acc.timestamps.push(data.timestamp);
          return acc;
        },
        {
          bloodOxygen: [],
          heartrate: [],
          pressureHigh: [],
          pressureLow: [],
          step: [],
          temperature: [],
          timestamps: []
        }
      );
    } else {
      throw new Error('Data is not in expected format or success flag is false');
    }
  } catch (error) {
    console.error('Failed to fetch health data:', error);
  }
}
onMounted(fetchHealthData);
</script>

<template>
  <NSpace vertical :size="16">
    <HeaderBanner />
    <CardData />
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- 以HeartRateChart为例，传递heartrate数据和timestamps -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <HeartRateChart :data="healthData.heartrate" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <TemperatureChart :data="healthData.temperature" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
    </NGrid>
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- 以HeartRateChart为例，传递heartrate数据和timestamps -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <BloodOxygenChart :data="healthData.bloodOxygen" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <StepChart :data="healthData.step" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
    </NGrid>
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- 以HeartRateChart为例，传递heartrate数据和timestamps -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <PressureHighChart :data="healthData.pressureHigh" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <PressureLowChart :data="healthData.pressureLow" :timestamps="healthData.timestamps" />
        </NCard>
      </NGi>
    </NGrid>
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- 以HeartRateChart为例，传递heartrate数据和timestamps -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <GaugeChart :data="healthData.temperature" />
        </NCard>
      </NGi>
    </NGrid>
  </NSpace>
</template>

<style scoped></style>
