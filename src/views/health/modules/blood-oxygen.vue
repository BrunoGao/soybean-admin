<script setup lang="ts">
import { toRaw, watchEffect } from 'vue';
import { $t } from '@/locales';

import { useEcharts } from '@/hooks/common/echarts';

defineOptions({
  name: 'BloodOxygenChart'
});

const props = withDefaults(
  defineProps<{
    data: number[];
    timestamps: string[];
  }>(),
  {
    data: () => [],
    timestamps: () => []
  }
);
watchEffect(() => {
  console.log('heartrate2', toRaw(props.data));
});
const { domRef, updateOptions } = useEcharts(() => ({
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross',
      label: {
        backgroundColor: '#6a7985'
      }
    }
  },
  legend: {
    data: [$t('page.health.bloodOxygen')]
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    boundaryGap: false,
    data: toRaw(props.timestamps)
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      color: '#26deca', // 温度使用的颜色，例如暗橙色，体现出温暖的感觉
      name: $t('page.health.bloodOxygen'),
      type: 'line',
      smooth: true,
      stack: 'Total',
      areaStyle: {
        color: {
          type: 'linear',
          x: 0,
          y: 0,
          x2: 0,
          y2: 1,
          colorStops: [
            {
              offset: 0.25,
              color: '#26deca' // 温度渐变起始颜色
            },
            {
              offset: 1,
              color: '#fff' // 渐变结束颜色
            }
          ]
        }
      },
      emphasis: {
        focus: 'series'
      },
      data: toRaw(props.data)
    }
  ]
}));
async function init() {
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });

  updateOptions(opts => {
    opts.xAxis.data = props.timestamps;
    opts.series[0].data = props.data;

    return opts;
  });
}

init();
</script>

<template>
  <NCard :bordered="false" class="card-wrapper">
    <div ref="domRef" class="h-360px overflow-hidden"></div>
  </NCard>
</template>

<style scoped></style>
