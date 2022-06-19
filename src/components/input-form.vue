<template>
  <div class="input-form">
    <div class="row">
      <span class="label">経過時間</span>
      <span>
        {{ eTime.toString().padStart(3, '0') }}
        分
      </span>
    </div>
    <div class="row">
      <span class="label">ミッション名</span>
      <VTextField
        v-model="missionName"
        @update:model-value="update('missionName', $event)"
        density="compact"
        variant="outlined"
      />
    </div>
    <div class="row">
      <span class="label">開始アーマー値</span>
      <VTextField
        v-model="startArmor"
        @update:model-value="update('startArmor', $event)"
        density="compact"
        variant="outlined"
      />
    </div>
    <div class="row">
      <span class="label">終了アーマー値</span>
      <VTextField v-model="endArmor" density="compact" variant="outlined" />
    </div>
    <div class="row">
      <VBtn @click="startRecord">start</VBtn>
      <VBtn @click="stopRecord">finish</VBtn>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, computed } from 'vue'

const props = defineProps({
  record: { type: Object, required: true },
})
const emits = defineEmits(['update:record'])
const update = (name, value) => {
  localStorage.setItem(`edf-armor-recorder--${name}`, value)
}

const missionName = ref(
  localStorage.getItem('edf-armor-recorder--missionName') || ''
)
const startArmor = ref(
  Number(localStorage.getItem('edf-armor-recorder--startArmor')) || 0
)
const endArmor = ref('')

const startDate = ref(0)
const finishDate = ref(0)
setInterval(() => (finishDate.value = new Date().getTime()), 100)
const eTime = computed(() =>
  Math.round(
    (finishDate.value - (startDate.value || finishDate.value)) / (1000 * 60)
  )
)

const doRecord = () => {
  const newRecord = {
    eTime: eTime.value,
    gotArmor: endArmor.value - startArmor.value,
  }
  emits('update:record', {
    ...props.record,
    [missionName.value]: [
      ...(props.record[missionName.value] ?? []),
      newRecord,
    ],
  })
  startDate.value = 0
}
const startRecord = () => {
  finishDate.value = startDate.value = new Date().getTime()
}
const stopRecord = () => {
  doRecord()
  startArmor.value = endArmor.value
  update('startArmor', startArmor.value)
  endArmor.value = ''
}
</script>

<style lang="scss" scoped>
.input-form {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  > .row {
    display: flex;
    align-items: center;
    gap: 1rem;
    > * {
      flex-shrink: 0;
      flex-grow: 1;
    }
    > .label {
      min-width: 20%;
      flex-grow: 0;
    }
  }
  ::v-deep(.v-input__details) {
    display: none;
  }
}
</style>
