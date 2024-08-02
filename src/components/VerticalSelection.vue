<template>
  <div class="flex gap-6 flex-wrap md:flex-col max-md:gap-y-2 max-md:justify-between max-md:text-sm">
    <div
      v-for="(option, idx) in options"
      :key="idx"
    >
      <div class="flex">
        <span
          class="flex-none"
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
