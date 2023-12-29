<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'
import ImageItem from './components/ImageItem.vue';
const tilingcontainer = ref<HTMLDivElement>()

// マス間の空き
const _gapsize = ref(1)

// 最大サイズ
const maxsize = ref(0)

// 表示する画像
const images = ref<string[]>([
])

// imagesからitemを消す。
function removeItem(index: number) {
  images.value.splice(index, 1)
}

// dndでファイルを受け取る。
function fileDrop(ev: Event) {
  if (!(ev instanceof DragEvent)) return
  const dt = ev.dataTransfer;
  if (dt?.items) {
    const arr = [...dt.items];
    arr.forEach((item) => {
      // ドロップしたものがファイルでない場合はなにもしない。
      if (item.kind === "file") {
        const file = item.getAsFile();
        if (file === null) return
        toUrl(file)
      }
    })
  } else if (dt?.files) {
    const arr = [...dt.files];
    arr.forEach((file) => {
      toUrl(file)
    });
  }
}

function toUrl(image: File) {
  const fr = new FileReader()

  fr.onload = () => {
    images.value.push(fr.result as string)
  }

  fr.onerror = () => {
    console.error(fr.error)
  }

  fr.readAsDataURL(image)
}

// マスのサイズを算定する。
const containerWidth = ref(0)
const containerHeight = ref(0)
const gapsize = computed(() => `${_gapsize.value * 4}px`)

function sizeChange() {
  if (tilingcontainer.value === undefined) return
  containerWidth.value = tilingcontainer.value.clientWidth
  containerHeight.value = tilingcontainer.value.clientHeight
}

const size = computed(() => {
  const imageNum = images.value.length
  if (imageNum === 0) return `${maxsize.value}px`
  const rowNum = Math.ceil(Math.sqrt(imageNum))
  const gap = _gapsize.value * 4
  let width = (containerWidth.value - (rowNum - 1) * gap) / rowNum
  if (maxsize.value > 0) {
    width = Math.min(maxsize.value, width)
  }
  return `${width}px`
})

onMounted(() => {
  window.addEventListener('resize', sizeChange)
  sizeChange()
})

onUnmounted(() => {
  window.removeEventListener('resize', sizeChange)
})
</script>

<template>
  <div>
    <div v-if="images.length === 0" class="tilingcontainer noimage" @drop.prevent="fileDrop"
      @dragover.prevent="() => { }">
      <p>drop image here.</p>
    </div>
    <div ref="tilingcontainer" class="tilingcontainer" @drop.prevent="fileDrop" @dragover.prevent="() => { }">
      <ul class="imagelist">
        <ImageItem v-for="(url, i) of images" :key="i" :url="url" :size="size" @remove="() => removeItem(i)" />
      </ul>
    </div>
  </div>
</template>

<style scoped>
.tilingcontainer {
  width: 100%;
  height: 100dvh;
  overflow: hidden;
}

.imagelist {
  margin: 0;
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  gap: v-bind(gapsize);
  line-height: 0;
}

.noimage {
  display: flex;
  position: absolute;
  justify-content: center;
  align-items: center;
}
</style>
