<template>
  <div :class="['background-image', `background-image--${genre}`]">
    <transition name="fade">
      <div v-if="genre === 'general'" class="background-image__container background-image__container--general"></div>
      <div v-else-if="genre === 'fantasy'" class="background-image__container background-image__container--fantasy"></div>
      <div v-else-if="genre === 'pirates'" class="background-image__container background-image__container--pirates"></div>
      <div v-else-if="genre === 'sci-fi'" class="background-image__container background-image__container--sci-fi"></div>
      <div v-else-if="genre === 'western'" class="background-image__container background-image__container--western"></div>
      <div v-else-if="genre === 'mystery'" class="background-image__container background-image__container--mystery"></div>
    </transition>
  </div>
</template>

<script setup lang="ts">
interface Props {
  genre: string;
}

withDefaults(defineProps<Props>(), {
  genre: 'general',
})

</script>

<style scoped lang="scss">

.background-image {
  &__container {
    background-position: center bottom;
    background-repeat: no-repeat;
    background-size: cover;
    height: 100vh;
    position: fixed;
    width: 100vw;

    &--general {
      background-image: url('@/assets/background-general.jpeg');
      background-position: center bottom -100px;
    }

    &--fantasy {
      background-image: url('@/assets/background-fantasy.jpeg');
    }

    &--pirates {
      background-image: url('@/assets/background-pirates.jpeg');
    }

    &--sci-fi {
      background-image: url('@/assets/background-sci-fi.jpeg');
    }

    &--western {
      background-image: url('@/assets/background-western.jpeg');
    }

    &--mystery {
      background-image: url('@/assets/background-mystery.jpeg');
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

</style>
