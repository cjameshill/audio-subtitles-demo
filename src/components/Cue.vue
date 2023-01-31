<script setup>
import { ref, onMounted } from "vue";
import { useSubtitlesComposable } from "@/composables/subtitlesComposable";

const { y, top, offset } = useSubtitlesComposable();
const props = defineProps(["cue"]);
const cue = props.cue;
const cueItem = ref();
let cueItemY = 0;
const isSelected = ref(false);
cue.addEventListener("enter", (event) => {
  isSelected.value = true;
  console.log("scroll to: ", cueItemY - top.value);
  y.value = cueItemY - (top.value + offset);
});
cue.addEventListener("exit", (event) => {
  isSelected.value = false;
});
onMounted(() => {
  const cueItemBox = cueItem.value.getBoundingClientRect();
  cueItemY = cueItemBox.y;
});
</script>
<template>
  <p
    ref="cueItem"
    class="text-gray-500 text-lg font-normal"
    :class="{ 'font-bold text-gray-900': isSelected }"
  >
    {{ cue.text }}
  </p>
</template>
