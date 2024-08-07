<template>
  <div class="scatter-plot">
    <VSelect
      v-model="showMissionName"
      class="select"
      density="compact"
      variant="outlined"
      @update:model-value="update('showMissionName', $event)"
      :items="Object.keys(record)"
    />
    <Scatter
      class="chart"
      :chartData="chartData"
      :chartOptions="chartOptions"
    />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { Scatter } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement,
} from 'chart.js'
ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement
)

const showMissionName = ref(
  localStorage.getItem('edf-armor-recorder--showMissionName') || ''
)

const update = (name, value) => {
  localStorage.setItem(`edf-armor-recorder--${name}`, value)
}

const props = defineProps({
  record: {
    type: Object,
    required: true,
  },
})

const nums = computed(() => {
  const values = Object.values(props.record).flatMap((records) => records)
  const getNums = (nums) => {
    const sortedNums = [...nums].sort((a, b) => a - b)
    const minNum = sortedNums[0]
    const maxNum = sortedNums.at(-1)
    return {
      max: maxNum + maxNum * 0.03,
      min: minNum - maxNum * 0.03,
    }
  }
  const amounts = values.map((value) => value.gotArmor)
  const amountPerHs = values.map((value) => value.gotArmor / (value.eTime / 60))
  const times = values.map((value) => value.eTime)
  return {
    amount: getNums(amounts),
    amountPerH: getNums(amountPerHs),
    eTime: getNums(times),
  }
})
console.log({ props, nums: nums.value })

const chartData = computed(() => {
  const targetItems = props.record[showMissionName.value] ?? []
  return {
    datasets: [
      {
        label: '増加分',
        borderColor: '#f87979',
        backgroundColor: '#f87979',
        data: targetItems.map((item) => ({ x: item.eTime, y: item.gotArmor })),
        yAisID: 'amount',
      },
      {
        label: '増加分/h',
        borderColor: '#7acbf9',
        backgroundColor: '#7acbf9',
        data: targetItems.map((item) => ({
          x: item.eTime,
          y: item.gotArmor / (item.eTime / 60),
        })),
        yAxisID: 'amountPerH',
      },
    ],
  }
})

const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    amount: {
      position: 'left',
      max: nums.value.amount.max,
      min: nums.value.amount.min,
      ticks: {
        color: '#f87979',
      },
    },
    amountPerH: {
      position: 'right',
      max: nums.value.amountPerH.max,
      min: nums.value.amountPerH.min,
      ticks: {
        color: '#7acbf9',
      },
    },
    x: {
      max: nums.value.eTime.max,
      min: nums.value.eTime.min,
    },
  },
}))
</script>

<style lang="scss" scoped>
.scatter-plot {
  display: flex;
  flex-direction: column;
  > .select {
    ::v-deep(> .v-input__details) {
      display: none;
    }
  }
  > .chart {
    flex-grow: 1;
  }
}
</style>
