<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <div class="container mx-auto p-4 m-4">
    <img alt="Vue logo" class="w-12 h-12" src="@/assets/logo.svg" />
  </div>

  <RouterView />
</template>
<style>
.bdr {
  border: 1px solid red !important;
}
</style>
