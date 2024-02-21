<script setup lang="ts">


interface Param {
  left: string
  right: string
}

import { Ref, computed, onMounted, ref } from "vue"

import { useMousePressed } from '@vueuse/core'

let overContainer: Ref<HTMLElement | null> = ref(null)
let mainContainer: Ref<HTMLElement | null> = ref(null)
const dragbar: Ref<HTMLElement | null> = ref(null)


const { pressed } = useMousePressed({ target: dragbar })

const parameters: Ref<Param> = ref({
  left: "",
  right: ""
})

const move = () => {

  mainContainer.value!.addEventListener('mousemove', (event: any) => {
    if (pressed.value) {
      var rect = mainContainer.value!.getBoundingClientRect();
      var mouseX = event.clientX - rect.left + 2;
      overContainer.value!.style.width = mouseX + "px";
    }
  });

}

const rightImage = computed(() => {
  return "url(" + parameters.value.right + ")"
})
const leftImage = computed(() => {
  return "url(" + parameters.value.left + ")"
})




onMounted(() => {
  const rawparams = location.search

  const urlParams = new URLSearchParams(rawparams);

  console.log(urlParams.get("right"))

  parameters.value = {
    left: urlParams.get("left")!,
    right: urlParams.get("right")!
  }


  window.requestAnimationFrame(move)

})

</script>

<template>
  <main ref="mainContainer" :style="{ backgroundImage: rightImage }">
    <div ref="overContainer" class="over" :style="{ backgroundImage: leftImage }">

      <div ref="dragbar" class="dragbar"></div>
    </div>
  </main>
</template>

<style>
main {
  display: flex;
  width: 100%;
  height: 100%;
  align-items: start;
  justify-content: start;
  border-radius: 1rem;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
  position: relative;
  overflow: hidden;
  background-repeat: no-repeat;
  background-size: cover;
  background-attachment: fixed;
  border: solid 3px lightgray;

}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  user-select: none;
  -webkit-user-drag: none;
  background-attachment: fixed;
}

.over {
  display: flex;
  position: absolute;
  z-index: 4;
  width: 50%;
  height: 100%;
  max-width: 100%;
  resize: horizontal;
  overflow: visible;
  user-select: none;
  -webkit-user-drag: none;
  background-repeat: no-repeat;
  background-size: cover;
  background-attachment: fixed;
}

.dragbar {
  display: flex;
  right: 0;
  position: absolute;
  background-color: black;
  width: 8px;
  height: 100%;
  z-index: 10;
  border: ridge 1px black;
  cursor: ew-resize;
  user-select: none;
  -webkit-user-drag: none;
}
</style>
