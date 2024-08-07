<template>
  <div class="input-form">
    <div>
      <div class="row">
        <span class="label">経過時間</span>
        <span>
          {{
            Math.floor(eTime / 60)
              .toString()
              .padStart(3, '0')
          }}
          分
          {{ (eTime % 60).toString().padStart(2, '0') }}
          秒
        </span>
      </div>
      <div class="row">
        <span class="label">ミッション名</span>
        <VCombobox
          v-model="missionName"
          :items="Object.keys(record)"
          @update:model-value="update('missionName', $event)"
          density="compact"
          variant="outlined"
          no-filter
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
        <VBtn :disabled="Boolean(startDate)" @click="startRecord">start</VBtn>
        <VBtn
          :disabled="(Number(endArmor) || -Infinity) < startArmor || !startDate"
          @click="stopRecord"
          >finish</VBtn
        >
        <VBtn :disabled="!startDate" @click="reset">reset</VBtn>
      </div>
    </div>
    <div>
      <VTextarea
        class="textarea"
        variant="outlined"
        :model-value="JSON.stringify(record)"
        @update:model-value="$emit('update:record', JSON.parse($event))"
      />
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

const startDate = ref(
  Number(localStorage.getItem('edf-armor-recorder--startDate')) || 0
)
const finishDate = ref(new Date().getTime())
const updatingFinishDate = async (ms) => {
  if (ms) {
    const waitMs = ms - (new Date().getTime() % 1000)
    await new Promise((resolve) =>
      setTimeout(resolve, waitMs < 0 ? waitMs + 1000 : waitMs)
    )
  }
  return setInterval(
    () =>
      (finishDate.value = new Date().getTime(
        console.log(finishDate.value - (startDate.value || finishDate.value))
      )),
    1000
  )
}

const eTime = computed(() =>
  Math.round((finishDate.value - (startDate.value || finishDate.value)) / 1000)
)

let intervalIdPromise = startDate.value
  ? updatingFinishDate(startDate.value % 1000)
  : undefined

const reset = () => {
  startDate.value = 0
  finishDate.value = 0
  localStorage.setItem('edf-armor-recorder--startDate', startDate.value)
  intervalIdPromise.then((id) => clearInterval(id))
}
const doRecord = () => {
  const newRecord = {
    eTime: Math.round(eTime.value / 60),
    gotArmor: endArmor.value - startArmor.value,
  }
  emits('update:record', {
    ...props.record,
    [missionName.value]: [
      ...(props.record[missionName.value] ?? []),
      newRecord,
    ],
  })
}
const startRecord = () => {
  startDate.value = new Date().getTime()
  finishDate.value = startDate.value
  localStorage.setItem('edf-armor-recorder--startDate', startDate.value)
  intervalIdPromise = updatingFinishDate()
}
const stopRecord = () => {
  doRecord()
  startArmor.value = endArmor.value
  update('startArmor', startArmor.value)
  endArmor.value = ''
  reset()
}
</script>

<style lang="scss" scoped>
.input-form {
  display: flex;
  gap: 1rem;
  > div {
    width: 100%;
    height: 100%;
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
    > .textarea {
      display: block;
      ::v-deep(> div) {
        height: 100%;
        > div {
          height: 100%;
          .v-field__field {
            padding: 4px;
            > textarea {
              resize: none;
            }
          }
        }
      }
    }
  }
}
</style>
