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
        @click="$emit('select', idx)"
      >
        {{ option }}
      </span>
    </div>
  </div>
</template>

<script setup lang="ts">
import cursor from '@/assets/cursor.png'

import { onMounted } from 'vue'

type Option = string
const props = defineProps<{ options: Option[], selectedIndex: number }>()

const emit = defineEmits(['select'])

function handleKeyDown(e: KeyboardEvent) {
  e.preventDefault()
  let i = props.selectedIndex
  if (i || i === 0) {
    if (e.key === 'ArrowDown' && i < props.options.length - 1) {
      emit('select', i + 1)
    } else if (e.key === 'ArrowUp' && i > 0) {
      emit('select', i - 1)
    }
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown, false)
})

</script>
