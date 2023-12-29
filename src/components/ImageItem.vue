<script setup lang="ts">
import { ref } from 'vue';

defineProps<{
  url: string;
  size: string;
}>()

const emit = defineEmits<{
  remove: [];
}>()

// 全体表示か拡大表示か
const bgsize = ref<"cover" | "contain">("cover")

// ポインタが要素の上に載っているか
const isPointerHover = ref(false)

function pointerEnter(ev: Event) {
  if (!(ev instanceof PointerEvent)) return
  isPointerHover.value = true
}
function pointerLeave(ev: Event) {
  if (!(ev instanceof PointerEvent)) return
  isPointerHover.value = false
}
</script>

<template>
  <li class="item" @pointerenter="pointerEnter" @pointerleave="pointerLeave">
    <div class="deletebutton" :class="{ visible: isPointerHover }" @click="() => emit('remove')">x</div>
    <div class="image" :style="`background-image: url('${url}');`"></div>
  </li>
</template>

<style scoped>
.item {
  margin: 0;
  padding: 0;
  position: relative;
}

.image {
  width: v-bind(size);
  height: v-bind(size);
  background-size: v-bind(bgsize);
  background-position: center;
  background-repeat: no-repeat;
}

.deletebutton {
  background-color: #7888c0;
  width: 24px;
  height: 24px;
  display: none;
  justify-content: center;
  align-items: center;
  position: absolute;
  right: 8px;
  top: 8px;
  border-radius: 50%;
  color: white;
  border: 1px white solid;
  box-sizing: border-box;
  cursor: pointer;
}

.visible {
  display: flex;
}
</style>