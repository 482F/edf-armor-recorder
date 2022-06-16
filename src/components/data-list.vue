<template>
  <div class="data-list">
    {{ record }}
  </div>
</template>

<script setup>
const props = defineProps({
  record: { type: Object, required: true },
})
</script>

<style lang="scss" scoped>
.data-list {
  overflow-y: scroll;
}
</style>
