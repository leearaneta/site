<template>
  <div
    v-for="(option, idx) in options"
    :key="idx"
  >
    <div class="flex">
      <span
        class="flex-none mt-3 mb-3"
        :class="{ invisible: idx !== selectedIndex }"
      >
        <img width="36" :src="cursor"/>
      </span>
      <span
        class="cursor-pointer ml-2"
        @click="selectedIndex = idx"
      >
        {{ option }}
      </span>
    </div>
  </div>
</template>

<script setup lang="ts">
import cursor from '@/assets/cursor.png'
import cursormove from '@/assets/cursormove.mp3'

import { onMounted, ref, watch } from 'vue'

type Option = string
const props = defineProps<{ options: Option[] }>()

const emit = defineEmits(['select'])

const selectedIndex = ref<number>(0)

const audio = new Audio(cursormove)
function handleKeyDown(e: KeyboardEvent) {
  e.preventDefault()
  const i = selectedIndex.value
  if (i || i === 0) {
    if (e.key === 'ArrowDown' && i < props.options.length - 1) {
      selectedIndex.value = i + 1
    } else if (e.key === 'ArrowUp' && i > 0) {
      selectedIndex.value = i - 1
      emit('select', props.options[selectedIndex.value])
    }
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown, false)
})

watch(selectedIndex, () => {
  emit('select', props.options[selectedIndex.value])
  audio.play()
})

</script>
