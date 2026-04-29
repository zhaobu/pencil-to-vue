# Dashboard Patterns: Cards, Stats, Charts

Standard dashboard layout with statistic cards and charts.

## Statistic Cards

```vue
<template>
  <div class="stat-cards">
    <el-card v-for="item in stats" :key="item.key" shadow="never" class="stat-card">
      <div class="stat-card__icon" :style="{ backgroundColor: item.bgColor }">
        <el-icon :size="24" :color="item.color"><component :is="item.icon" /></el-icon>
      </div>
      <div class="stat-card__info">
        <span class="stat-card__value">{{ item.value }}</span>
        <span class="stat-card__label">{{ item.label }}</span>
      </div>
    </el-card>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Monitor, Finished, Warning } from '@element-plus/icons-vue'

interface StatItem {
  key: string
  label: string
  value: number
  icon: any
  color: string
  bgColor: string
}

const stats = ref<StatItem[]>([
  {
    key: 'host_online',
    label: '在线上位机',
    value: 12,
    icon: Monitor,
    color: 'var(--siro-success)',
    bgColor: 'rgba(82, 196, 26, 0.1)'
  },
  {
    key: 'task_today',
    label: '今日任务',
    value: 48,
    icon: Finished,
    color: 'var(--siro-primary)',
    bgColor: 'rgba(26, 115, 232, 0.1)'
  },
  {
    key: 'alert_today',
    label: '今日告警',
    value: 3,
    icon: Warning,
    color: 'var(--siro-danger)',
    bgColor: 'rgba(245, 34, 45, 0.1)'
  }
])
</script>

<style scoped lang="scss">
.stat-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
  margin-bottom: 16px;
}

.stat-card {
  :deep(.el-card__body) {
    display: flex;
    align-items: center;
    gap: 16px;
    padding: 20px;
  }
}

.stat-card__icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.stat-card__info {
  display: flex;
  flex-direction: column;
}

.stat-card__value {
  font-size: 24px;
  font-weight: 600;
  color: var(--siro-text-primary);
  line-height: 1.2;
}

.stat-card__label {
  font-size: 13px;
  color: var(--siro-text-muted);
  margin-top: 4px;
}
</style>
```

## Chart Card with ECharts

```vue
<template>
  <el-card shadow="never" class="chart-card">
    <template #header>
      <span class="chart-card__title">近7日任务趋势</span>
    </template>
    <v-chart class="chart" :option="chartOption" autoresize />
  </el-card>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import { use } from 'echarts/core'
import { CanvasRenderer } from 'echarts/renderers'
import { LineChart } from 'echarts/charts'
import { GridComponent, TooltipComponent } from 'echarts/components'
import VChart from 'vue-echarts'

use([CanvasRenderer, LineChart, GridComponent, TooltipComponent])

const dates = ref<string[]>(['04/22', '04/23', '04/24', '04/25', '04/26', '04/27', '04/28'])
const counts = ref<number[]>([12, 19, 15, 22, 18, 25, 20])

const chartOption = computed(() => ({
  tooltip: { trigger: 'axis' },
  grid: { left: 40, right: 20, top: 20, bottom: 30 },
  xAxis: {
    type: 'category',
    data: dates.value,
    axisLine: { lineStyle: { color: '#E5E7EB' } },
    axisLabel: { color: '#9CA3AF', fontSize: 12 }
  },
  yAxis: {
    type: 'value',
    axisLine: { show: false },
    axisTick: { show: false },
    splitLine: { lineStyle: { color: '#F3F4F6' } },
    axisLabel: { color: '#9CA3AF', fontSize: 12 }
  },
  series: [{
    type: 'line',
    data: counts.value,
    smooth: true,
    lineStyle: { color: 'var(--siro-primary)', width: 2 },
    areaStyle: {
      color: {
        type: 'linear',
        x: 0, y: 0, x2: 0, y2: 1,
        colorStops: [
          { offset: 0, color: 'rgba(26, 115, 232, 0.15)' },
          { offset: 1, color: 'rgba(26, 115, 232, 0)' }
        ]
      }
    },
    itemStyle: { color: 'var(--siro-primary)' }
  }]
}))
</script>

<style scoped lang="scss">
.chart-card__title {
  font-size: 15px;
  font-weight: 600;
  color: var(--siro-text-primary);
}

.chart {
  height: 280px;
  width: 100%;
}
</style>
```

## Pie Chart

```vue
<template>
  <el-card shadow="never">
    <template #header>
      <span>设备状态分布</span>
    </template>
    <v-chart class="pie-chart" :option="pieOption" autoresize />
  </el-card>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import { use } from 'echarts/core'
import { CanvasRenderer } from 'echarts/renderers'
import { PieChart } from 'echarts/charts'
import { TooltipComponent, LegendComponent } from 'echarts/components'
import VChart from 'vue-echarts'

use([CanvasRenderer, PieChart, TooltipComponent, LegendComponent])

const pieData = ref([
  { name: '在线', value: 12, itemStyle: { color: 'var(--siro-success)' } },
  { name: '离线', value: 3, itemStyle: { color: '#D1D5DB' } },
  { name: '故障', value: 1, itemStyle: { color: 'var(--siro-danger)' } }
])

const pieOption = computed(() => ({
  tooltip: { trigger: 'item', formatter: '{b}: {c} ({d}%)' },
  legend: { bottom: 0, itemWidth: 10, itemHeight: 10, textStyle: { fontSize: 12 } },
  series: [{
    type: 'pie',
    radius: ['45%', '70%'],
    center: ['50%', '45%'],
    avoidLabelOverlap: true,
    label: { show: false },
    data: pieData.value
  }]
}))
</script>

<style scoped lang="scss">
.pie-chart {
  height: 260px;
  width: 100%;
}
</style>
```

## Key Points

- Always use `autoresize` on `v-chart` for responsive behavior
- Use CSS custom properties in chart colors via `var(--xxx)`
- Use `shadow="never"` on cards for flat design
- Place chart height in CSS, not inline style
- Use `computed` for chart options so they react to data changes
- Use `grid` to control chart padding inside the container
