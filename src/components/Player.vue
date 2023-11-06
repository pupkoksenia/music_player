<template>
  <div class="player">
    <div class="player__body">
      <div class="box switch">
        <img src="../assets/next.svg" width="30" height="30" @click="setNext" />
        <audio id="audio" ref="audioRef" :src="audioUrl"></audio>
        <img
          v-if="!isPlaying"
          src="../assets/player.svg"
          width="30"
          height="30"
          @click="startPlay"
        />
        <img
          v-else
          src="../assets/pause.svg"
          width="30"
          height="30"
          @click="stopPlay"
        />
        <img
          src="../assets/next.svg"
          width="30"
          height="30"
          style="transform: rotate(180deg)"
          @click="setPrevious"
        />
      </div>

      <div class="box music">
        <progress
          id="sound"
          class="music__progress"
          max="100"
          :value="progressSound"
        ></progress>
        <div class="music__audioName">{{ audioName }}</div>
        <progress
          id="time"
          class="music__progress"
          max="100"
          :value="progressTime"
        ></progress>
      </div>
      <div class="box animation">
        <img src="../assets/party.svg" width="50" height="50" />
        <img
          class="animation__disk"
          :class="isPlaying ? 'rot' : ''"
          src="../assets/disk.svg"
          width="80"
          height="80"
        />
        <img src="../assets/party.svg" width="50" height="50" />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, watch } from 'vue';
import { arrayMusicNames } from '@/consts/index';
import '../style/player.scss';

export default defineComponent({
  name: 'PlayerComponent',
  setup() {
    const audioRef = ref();
    const isPlaying = ref(false);
    /* eslint @typescript-eslint/no-var-requires: "off" */
    const audioUrl = ref();
    const audioId = ref();
    const progressTime = ref(0);
    const progressSound = ref(0);
    const audioName = ref();

    const timePlay = ref();
    const partTimePlay = ref(10);
    const stopIdInterval = ref();

    const arraySegmentation = Array.from({ length: 21 }, (_, i) => i);

    onMounted(() => {
      audioName.value = arrayMusicNames[0];
      audioId.value = 0;
      audioUrl.value = require('../data/music/' + audioName.value);
    });

    watch(audioName, (newVal) => {
      audioUrl.value = require('../data/music/' + newVal);
    });

    const setTimePlay = () => {
      timePlay.value = audioRef.value?.duration;
    };

    const setProgressSound = () => {
      progressSound.value = audioRef.value?.volume * 100;
    };

    const setProgressTime = () => {
      const id = setInterval(check, 10000);
      function check() {
        if (partTimePlay.value >= Math.round(timePlay.value)) {
          clearInterval(id);
        } else {
          progressTime.value = Math.round(
            (partTimePlay.value / timePlay.value) * 100
          );
          partTimePlay.value += 10;
        }
      }
      return id;
    };

    const startPlay = () => {
      isPlaying.value = true;
      audioRef.value?.play();
      setTimePlay();
      setProgressSound();
      stopIdInterval.value = setProgressTime();
    };

    const stopPlay = () => {
      isPlaying.value = false;
      audioRef.value?.pause();
      clearInterval(stopIdInterval.value);
    };

    const setNext = () => {
      clearValues();
      let nextIndex = audioId.value + 1;
      if (nextIndex === arrayMusicNames.length) {
        audioName.value = arrayMusicNames[0];
        audioId.value = 0;
      } else {
        audioName.value = arrayMusicNames[nextIndex];
        audioId.value = nextIndex;
      }
      stopPlay();
    };

    const setPrevious = () => {
      clearValues();
      let len = arrayMusicNames.length;
      let nextIndex = audioId.value - 1;
      if (nextIndex === -1) {
        audioName.value = arrayMusicNames[len - 1];
        audioId.value = len - 1;
      } else {
        audioName.value = arrayMusicNames[nextIndex];
        audioId.value = nextIndex;
      }
      stopPlay();
    };

    const clearValues = () => {
      clearInterval(stopIdInterval.value);
      partTimePlay.value = 10;
      progressTime.value = 0;
      progressSound.value = 0;
    };

    return {
      audioRef,
      audioUrl,
      isPlaying,
      progressTime,
      progressSound,
      audioName,
      audioId,
      arraySegmentation,
      startPlay,
      stopPlay,
      setNext,
      setPrevious,
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss"></style>
