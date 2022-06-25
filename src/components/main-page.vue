<template>
  <div class="main">
    <InputForm
      class="input-form"
      :record="record"
      @update:record="updateRecord"
    />
    <ScatterPlot class="scatter-plot" :record="record" />
  </div>
</template>

<script setup>
import InputForm from './input-form.vue'
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
  display: flex;
  flex-direction: column;
  gap: 1rem;
  height: 100vh;
  width: 100vw;
  > .input-form {
    flex-grow: 0;
    flex-shrink: 1;
  }
  > .scatter-plot {
    flex-grow: 1;
    flex-shrink: 0;
  }
}
</style>
