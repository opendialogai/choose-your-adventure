<template>
  <BackgroundImage :genre="genre"></BackgroundImage>
  <div class="wrapper">
    <MainHeading :genre="genre"></MainHeading>
    <section class="reading">
      <div class="reading__pane">
        <div :class="['reading__pane-info', {'reading__pane-info--expanded': expanded}]">
          <div class="reading__world">
            <h2>The World</h2>
            <TypingText :text-blocks="world" :once="true"></TypingText>
            <img v-if="!world.length" class="reading__no-content" src="@/assets/thinking-bubble.svg" alt="">
          </div>
          <div class="reading__character">
            <h2>Your Character</h2>
            <TypingText :text-blocks="character" :once="true"></TypingText>
            <img v-if="!character.length" class="reading__no-content" src="@/assets/thinking-bubble.svg" alt="">
          </div>
          <button class="reading__pane-expand" @click="expanded = !expanded">{{ expanded ? 'x' : '>' }}</button>
        </div>
        <div class="reading__pane-text">
          <h2>Your Adventure</h2>
          <TypingText :text-blocks="scenes" :sequential="true" :currentString="currentScene - 1" @complete="renderOptions"></TypingText>
          <p v-if="!scenes.length">Your story awaits! Use the controls at the bottom of the screen to generate an adventure based on your own preferences and choices.</p>
          <div class="reading__pane-anchor"></div>
        </div>
      </div>
    </section>
    <section v-show="showControls" class="controls">
      <div class="controls__text-window">
        <div class="controls__instructions">
          <TypingText :text-blocks="instructions" :emptyOnUpdate="true" :speed="40"></TypingText>
        </div>
      </div>
      <div class="controls__panel">
        <div id="od-webchat" :class="{'od-webchat--thinking': thinking}"></div>
        <ThinkingBar v-if="showThinkingTexts" :textBlocks="thinkingTexts"></ThinkingBar>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onBeforeMount, ref } from 'vue'
import MainHeading from './components/MainHeading.vue'
import BackgroundImage from './components/BackgroundImage.vue'
import TypingText from './components/TypingText.vue'
import ThinkingBar from './components/ThinkingBar.vue'

const thinking = ref(false);
const scenes = ref([]);
const instructions = ref([]);
const options = ref(null);
const showControls = ref(false);
const world = ref([]);
const character = ref([]);
const currentScene = ref(0);
const genre = ref('general');
const expanded = ref(false);
let timer = null
const thinkingTexts = [
  'Creating atmosphere...',
  'Getting your character into trouble...',
  'Juggling plots...',
  'Generating options...',
  'Finding... the right... words...',
  'Stirring up tension...',
  'Hatching plans...',]
const showThinkingTexts = ref(false)
const settings = {
    url: 'https://fantastic-fruitbat.staging-cloud.opendialog.ai',
    appKey: '3377699721000000|wVsfKivU4aqEAzZs4ZXwsRGrOMoSHNpEPZ9Y3VDBf35ba03f',
    sdkEnabled: true,
    iframe: false,
    fullScreen: true,
    language: 'en',
    user: {
      custom: {
        selected_scenario: '3377699723000023'
      }
    }
  }
const webchat = new ODWebChat.OpenDialogChatSDK('#od-webchat')

onBeforeMount(() => {
  sessionStorage.clear()
})

onMounted(() => {
  webchat.on('messageReceived', (e) => {
    const arr = Array.isArray(e.detail.messages) ? e.detail.messages: [e.detail.messages]
    processMessages(arr) 
  })

  webchat.on('messageSent', (e) => {
    thinking.value = true
    instructions.value = []
    options.value = null
    timer = setInterval(() => {
      showThinkingTexts.value = true
    }, 1000);

    if (e.detail.message.content.callback_id === 'confirmGenreChoice') {
      genre.value = e.detail.message.content.data.text.toLowerCase()
    }
  })

  webchat.on('loaded', () => {
   setTimeout(() => {
      showControls.value = true
    }, 500)
  })

  webchat.init(settings)
}) 

const processMessages = (messages: string[]) => {
  const storySetting = messages.find((msg) => msg.type === 'meta' && msg.data.elements.storySetting)
  const characterProfile = messages.find((msg) => msg.type === 'meta' && msg.data.elements.characterProfile)
  
  if (storySetting && !world.value.length) {
    world.value.push(storySetting.data.elements.storySetting)
  }
  
  if (characterProfile && !character.value.length) {
    character.value.push(characterProfile.data.elements.characterProfile) 
  }

  if (messages.some((msg) => msg.type === 'meta' && msg.data.elements.scene)) {
    messages.forEach((message, index) => {
      if (message.type === 'text' && message.data.text) {
        scenes.value.push(message.data.text)
        currentScene.value ++
      } else if (message.type === 'button' && message.data.text) {
        options.value = message.data.text
      }
    })
    return 
  }

  messages.forEach((message, index) => {
    if (message.data.text) {
      instructions.value.push(message.data.text)
    }
  })

  clearInterval(timer)
  showThinkingTexts.value = false
  setTimeout(() => {thinking.value = false}, 50)
}

const renderOptions = () => {
  clearInterval(timer)
  showThinkingTexts.value = false
  setTimeout(() => {thinking.value = false}, 50)

  if (options.value) {
    instructions.value.push(options.value)
  }
}

</script>

<style scoped lang="scss">
  .wrapper {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    height: 100vh;
    justify-content: flex-start;
    margin: 0 auto;
    max-width: 1280px;
    padding: 0 1rem 2rem;
    position: relative;
  }

  .reading {
    display: flex;
    justify-content: center;
    height: 55vh;
    margin-top: -3rem;

    h2 {
      font-family: "Lilita One", sans-serif;
      font-size: 2rem;
      font-weight: 400;
      line-height: 1;
      margin-bottom: 1rem;
    }

    &__pane {
      background-color: rgba(255, 255, 255, 0.8);
      border-radius: 4px;
      box-shadow: 0 0 1rem 0 rgba(0, 0, 0, 0.2);
      overflow: hidden;
      padding: 1.5rem 1rem 1rem 2rem;
      position: relative;
      width: 100%;

      @media screen and (min-width: 768px) {
        display: flex;
        gap: 2rem;
        padding: 1.5rem;
      }
    }

    &__pane-text {
      height: 100%;
      line-height: 1.6;
      overflow: auto;
      padding: 0 0.5rem 0.5rem 0;
    }

    &__pane-text * {
      overflow-anchor: none;
    }

    &__pane-anchor {
      overflow-anchor: auto;
      height: 1px;
    }

    &__pane-info {
      background: white;
      display: flex;
      flex-direction: column;
      gap: 1rem;
      height: 100%;
      left: 0;
      padding: 1rem;
      position: absolute;
      top: 0;
      transform: translateX(-100%);

      @media screen and (min-width: 768px) {
        background: transparent;
        min-width: 25%;
        max-width: 25%;
        padding: 0;
        position: static;
        transform: none;
      }

      &--expanded {
        transform: translateX(0);

        .reading__pane-expand {
          right: 0;
          border-radius: 50% 0 0 50%;
        }
      }
    }

    &__world,
    &__character {
      flex-basis: 50%;
      font-size: 14px;
      line-height: 1.6;
      overflow: auto;
    }

    &__no-content {
      max-width: 30%;
      opacity: 0.4;

      @media screen and (min-width: 768px) {
        max-width: 40%;
        padding: 1rem;
      }
    }

    &__pane-expand {
      background: white;
      border: none;
      border-radius: 0 50% 50% 0;
      box-shadow:  0 0 1rem 0 rgba(0, 0, 0, 0.2);
      color: $primaryText;
      cursor: pointer;
      font-size: 1.5rem;
      height: 1.8rem;
      position: absolute;
      right: -1.5rem;
      top: 1rem;
      width: 1.8rem;

      @media screen and (min-width: 768px) {
        display: none;
      }
    }
  }

  .controls {
    align-self: center;
    display: flex;
    flex-direction: column;
    margin-top: auto;
    max-height: 25vh;
    max-width: 800px;
    padding-bottom: 2rem;
    width: 100%;

    &__text-window {
      background-color: rgba(0, 0, 0, 0.8);
      border-radius: 16px 16px 0 0;
      box-shadow: 0 0 1rem 0 rgba(0, 0, 0, 0.2);
      color: white;
      flex-basis: 45%;
      flex-grow: 1;
      font-family: "Courier Prime", monospace;
      font-weight: 400;
      font-style: normal;
      min-height: 100px;
      max-height: calc(20vh - 100px);
      overflow: auto;
      padding: 1rem;
      width: 100%;
    }

    &__panel {
      position: relative;
    }

    #od-webchat {
      min-height: 100px;
      width: 100%;
    }

    .thinking-bar {
      position: absolute;
      text-align: center;
      top: 50%;
      transform: translateY(-50%);
      width: 100%;
    }
  }
</style>
