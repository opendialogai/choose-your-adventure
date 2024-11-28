<template>
  <div :class="['background-image', `background-image--${genre}`]">

  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

const background = computed(() => new URL(`../assets/background-${props.genre}.jpeg`, import.meta.url).href)
const backgroundVar = computed(() => `url(${background.value})`)

interface Props {
  genre: string;
}

const props = withDefaults(defineProps<Props>(), {
  genre: 'general',
})

</script>

<style scoped lang="scss">
.background-image {
  background-image: v-bind('backgroundVar');
  background-position: center bottom;
  background-repeat: no-repeat;
  background-size: cover;
  height: 100vh;
  position: fixed;
  width: 100vw;

  &--general {
    background-position: center bottom -100px;
  }
}
</style>
