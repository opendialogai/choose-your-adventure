<template>
  <div class="typing-text">
    <p :class="`typeit-container--${uuid}`"></p>
  </div>
</template>

<script setup lang="ts">
import TypeIt from 'typeit';
import { ref, watchEffect, onMounted, watch } from 'vue';

const uuid = ref(self.crypto.randomUUID());
let typeitInstance = null

interface props {
  textBlocks: string[],
  emptyOnUpdate?: boolean,
  currentString?: number | null,
  sequential?: boolean,
  once?: boolean,
  speed?: number,
}

const emit = defineEmits(['complete'])

const props = withDefaults(defineProps<props>(), {
  emptyOnUpdate: false,
  sequential: false,
  once: false,
  speed: 20,
})

watch(() => props.textBlocks, (newVal, oldVal) => {
  let arr

  if (typeitInstance && typeitInstance.is('started') && !props.textBlocks.length) {
    typeitInstance.reset()
    typeitInstance.type('').flush(() => {return})
  } else if (typeitInstance && props.textBlocks.length > 0) {
    if (props.emptyOnUpdate) {
      typeitInstance.empty()
    }

    if (props.currentString) {
      arr = props.textBlocks.slice(props.currentString)
    } else {
      arr = props.textBlocks
    }

    if (props.sequential) {
      arr.forEach((block, i) => {
        if (props.currentString === 0) {
          typeitInstance.type(block).flush(() => {return});
        } else {
          typeitInstance.break().break().type(block).flush(() => {return});
        }
      })
    } else {
      arr.forEach((block, i) => {
        if (i === arr.length - 1 && !props.currentString) {
          typeitInstance.type(block).flush(() => {return});
        } else {
          typeitInstance.type(block).break().break().flush(() => {return});
        }
      })
    }
  }
  
}, {deep: true})

onMounted(() => {
  const typeitContainer = document.querySelector(`.typeit-container--${uuid.value}`);
  if (typeitContainer) {
    typeitInstance = new TypeIt(typeitContainer, {
      speed: props.speed,
      waitUntilVisible: true,
      afterString: (step, instance) => {
        if (props.once) {
          instance.destroy()
        }
        emit('complete')
      }
    });
  }
})

</script>

<style scoped lang="scss">

</style>
