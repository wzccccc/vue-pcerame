<template>
  <div id="drag-con">
    <h2 v-for="item in 30" :key="item">拖拽组件{{ item }}</h2>
  </div>
</template>

<script setup lang="ts" name="dragCon"></script>

<style scoped lang="scss">
@use "./index.scss";
</style>
