<template>
  <h1 :class="['main-heading', `main-heading--${genre}`]">
    <span class="main-heading__small">Choose Your Own</span>
    Adven<svg class="main-heading__icon" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xml:space="preserve" version="1.1" style="shape-rendering:geometricPrecision;text-rendering:geometricPrecision;image-rendering:optimizeQuality;" viewBox="0 0 176 550" x="0px" y="0px" fill-rule="evenodd" clip-rule="evenodd"><defs>
    </defs><g><path fill="black" d="M88 545c21,-38 34,-68 55,-106l-31 -289 48 0c9,0 16,-7 16,-16 0,-9 -7,-17 -16,-17l-51 0 -1 -100 17 0c4,0 8,-4 8,-9 0,-4 -4,-8 -8,-8l-74 0c-4,0 -8,4 -8,8 0,5 4,9 8,9l17 0 -1 100 -51 0c-9,0 -16,8 -16,17 0,9 7,16 16,16l47 0 -30 289c20,38 34,68 55,106z"/></g></svg>ure
  </h1>
</template>

<script setup lang="ts">
interface Props {
  genre: string;
}

const props = withDefaults(defineProps<Props>(), {
  genre: 'general',
})

</script>

<style scoped lang="scss">
.main-heading {
  color: $primaryText;
  font-family: "Lilita One", sans-serif;
  font-weight: 400;
  font-size: 6rem;
  font-style: normal;
  position: relative;
  text-transform: uppercase;
  top: -3.5rem;
  z-index: 10;

  &__small {
    font-size: 2rem;
    left: 0;
    position: absolute;
    top: 5.5rem;
  }

  &__icon {
    position: relative;
    top: 4rem;
    width: 4rem;

    path {
      fill: $primaryText;
    }
  }

  &--pirates {
    color: white;

    .main-heading__icon path {
      fill: white;
    }
  }


  @media screen and (max-height: 901px) {
    font-size: 4rem;

    .main-heading__small {
      font-size: 1.3rem;
      top: 4.6rem;
    }

    .main-heading__icon {
      top: 4rem;
      width: 3rem;
    }
  }

}
</style>
