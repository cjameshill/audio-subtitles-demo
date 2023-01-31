<script setup>
import { onMounted, ref, computed } from "vue";
// import Hls from "hls.js";
const audioRef = ref();
const isSelected = ref("1");
/*
Methods
 */
const audioLoaded = (event) => {
  console.log("audio loaded: ", audioRef.value?.duration);
};
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
    });
  }
  audioRef.value.onplay = () => {
    console.log(audioRef.value.textTracks[0].cues);
    audioRef.value.textTracks[0].cues.map((cue) => {
      cue.addEventListener("enter", (event) => {
        isSelected.value = cue.id;
      });
    });
    audioRef.value.textTracks[0].cues[2].addEventListener("enter", (event) => {
      console.log("cue is showing");
    });
  };

  return "loadHlsAudio";
};
const cues = computed(() => audioRef?.value?.textTracks?.[0]?.cues || []);
onMounted(async () => {
  loadHlsAudio({
    streamingUrl:
      "https://audios-prod.cdn.urloapp.com/27jgIbd45RUcPbkalzT8joxa8Ue/62568ade65e17e000f6c0900/62568ade65e17e000f6c0900_1.m3u8",
  });
});
</script>

<template>
  <div class="container mx-auto">
    <h1 class="text-2xl">Welcome</h1>
    <div class="flex">
      <video ref="audioRef" controls crossorigin="anonymous" preload="metadata">
        <track
          label="English"
          kind="subtitles"
          srclang="en"
          src="/62568ade65e17e000f6c0900_1_static.mp4.vtt"
          default
        />
      </video>
      <!-- <audio
        ref="audioRef"
        preload="metadata"
        controls
        @loadedmetadata="audioLoaded"
      ></audio> -->
    </div>
    <div class="flex flex-col">
      <p
        v-for="cue in cues"
        :key="cue.id"
        :class="{ 'font-bold': isSelected === cue.id }"
      >
        {{ cue.text }}
      </p>
    </div>
  </div>
</template>
<style>
video::cue {
  background-image: linear-gradient(to bottom, dimgray, lightgray);
  color: papayawhip;
  position: fixed;
  top: 200px;
}
</style>
