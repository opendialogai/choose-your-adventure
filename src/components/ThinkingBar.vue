<template>
  <div class="thinking-bar">
    <p :class="`typeit-container--${uuid}`"></p>
  </div>
</template>

<script setup lang="ts">
import TypeIt from 'typeit';
import { ref, watchEffect, onMounted, watch } from 'vue';

const uuid = ref(self.crypto.randomUUID());
let typeitInstance = null

const props = defineProps({
  textBlocks: Array,
})

const randomInteger = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

onMounted(() => {
  const typeitContainer = document.querySelector(`.typeit-container--${uuid.value}`);
  const randomStartPoint = randomInteger(0, props.textBlocks.length);
  let arr = [...props.textBlocks];
  const tmp = arr.splice(0, randomStartPoint);
  arr = arr.concat(tmp);

  if (typeitContainer) {
    typeitInstance = new TypeIt(typeitContainer, {
      strings: arr,
      loop: true,
      breakLines: false,
      speed: 150,
      nextStringDelay: 100,
      waitUntilVisible: true,
    }).go();
  }
})

</script>

<style scoped lang="scss">

</style>
