<script setup lang="ts">
import { ref } from 'vue';

defineProps<{
  url: string;
  size: string;
}>()

// 全体表示か拡大表示か
const bgsize = ref<"cover" | "contain">("cover")
</script>

<template>
  <li class="item">
    <div>
      <div class="image" :style="`background-image: url('${url}');`"></div>
    </div>
  </li>
</template>

<style scoped>
.item {
  margin: 0;
  padding: 0;
}

.image {
  width: v-bind(size);
  height: v-bind(size);
  background-size: v-bind(bgsize);
  background-position: center;
  background-repeat: no-repeat;
}
</style>