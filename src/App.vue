<template>
  <VApp>
    <VMain>
      <MainPage/>
    </VMain>
  </VApp>
</template>

<script setup>
import MainPage from './components/main-page.vue'
</script>

<style lang="scss">
* {
  font-family: 'Cica';
  &::-webkit-scrollbar-track {
    background-color: #eee;
  }
  &::-webkit-scrollbar {
    width: 10px;
    height: 10px;
  }
  &::-webkit-scrollbar-thumb {
    background-color: #ccc;
    border-radius: 30px;
  }
}
</style>
