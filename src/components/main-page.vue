<template>
  <div class="main">
    <InputForm
      class="input-form"
      :record="record"
      @update:record="updateRecord"
    />
    <DataList class="data-list" :record="record" />
    <ScatterPlot class="scatter-plot" :record="record" />
  </div>
</template>

<script setup>
import InputForm from './input-form.vue'
import DataList from './data-list.vue'
import ScatterPlot from './scatter-plot.vue'
import { ref } from 'vue'

const record = ref(
  JSON.parse(localStorage.getItem('edf-armor-recorder--record') || '{}')
)

const updateRecord = (newRecord) => {
  record.value = newRecord
  localStorage.setItem('edf-armor-recorder--record', JSON.stringify(newRecord))
}
</script>

<style lang="scss" scoped>
.main {
  padding: 1rem;
  display: grid;
  grid-template-rows: 25% 1fr;
  grid-template-columns: 50% 50%;
  height: 100vh;
  width: 100vw;
  > .input-form {
    grid-row: 1;
    grid-column: 1;
  }
  > .data-list {
    grid-row: 1;
    grid-column: 2;
  }
  > .scatter-plot {
    grid-row: 2;
    grid-column: 1 / 3;
  }
}
</style>
