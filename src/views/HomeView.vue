<script setup>
import { onMounted, ref, computed } from "vue";
import { useSubtitlesComposable } from "@/composables/subtitlesComposable";
import Cue from "@/components/Cue.vue";
const { cuesHolder } = useSubtitlesComposable();
// import Hls from "hls.js";
const audioRef = ref();
const cues = ref([]);
const cueList = computed(() => cues.value);
const audioLoaded = ref(false);
/*
Methods
 */
const loadHlsAudio = ({ streamingUrl }) => {
  if (Hls.isSupported()) {
    const hls = new Hls();
    console.log("hls is supported: ", streamingUrl);
    if (audioRef.value) {
      hls.attachMedia(audioRef.value);
    }
    hls.on(Hls.Events.MEDIA_ATTACHED, () => {
      console.log("media attached");
      hls.loadSource(streamingUrl);
      for (let i = 0; i < audioRef.value.textTracks.length; i++) {
        audioRef.value.textTracks[i].mode = "showing";
        console.log("cuelist: ", audioRef.value.textTracks[i]);
        console.log("index: ", i);
        cues.value = audioRef.value.textTracks[i].cues;
      }
    });
  }
  return "loadHlsAudio";
};
onMounted(async () => {
  loadHlsAudio({
    streamingUrl:
      "https://audios-prod.cdn.urloapp.com/27jgIbd45RUcPbkalzT8joxa8Ue/62568ade65e17e000f6c0900/62568ade65e17e000f6c0900_1.m3u8",
  });
});
</script>

<template>
  <div class="container px-4 mx-auto">
    <h1 class="text-2xl my-4">Tonto</h1>
    <div class="flex">
      <audio
        ref="audioRef"
        controls
        crossorigin="anonymous"
        preload="metadata"
        @loadedmetadata="audioLoaded = true"
      >
        <track
          label="English"
          kind="subtitles"
          srclang="en"
          src="https://transcriptions-staging.cdn.gettonto.com/27jgIbd45RUcPbkalzT8joxa8Ue/62568ade65e17e000f6c0900/62568ade65e17e000f6c0900_1_static.mp4.vtt"
          default
        />
      </audio>
    </div>
    <div
      v-if="audioLoaded"
      ref="cuesHolder"
      class="flex flex-col mt-8 h-[300px] overflow-y-scroll scroll-smooth"
    >
      <div v-for="cue in cueList" :key="cue.id" class="">
        <Cue :cue="cue"></Cue>
      </div>
    </div>
  </div>
</template>
<style>
::cue {
  display: none;
}
</style>
