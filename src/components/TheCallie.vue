<template>
  <div class="flex h-full justify-center">
    <div v-if="urls.length > 0" class="flex w-full" ref="container">
      <Transition :key="imageIndex" :name="transition" appear>
        <div
          class="flex flex-wrap justify-evenly cursor-pointer"
          @click="transitionForward()"
          @touchstart="touchstartX = $event.changedTouches[0].screenX"
          @touchend="onTouchEnd"
        >
          <img
            class="image"
            v-for="(url, i) in urls"
            v-show="i == imageIndex"
            :class="containerType"
            :loading="i < loadedImageCount ? 'eager' : 'lazy'"
            :key="url"
            :src="url"
          />
        </div>
      </Transition>
    </div>
  </div>

</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref, watch } from 'vue';
import cursormove from '@/assets/cursormove.mp3'

const props = defineProps<{ urls: string[], chunkSize: number }>()
const imageIndex = ref(0)
const loadedImageCount = ref(props.chunkSize)
const transition = ref('forward')

const audio = new Audio(cursormove)
function transitionForward() {
  transition.value = 'forward'
  if (imageIndex.value === props.urls.length - 1) {
    imageIndex.value = 0
  } else {
    imageIndex.value += 1
  }
  audio.play()
}

function transitionBackward() {
  transition.value = 'backward'
  if (imageIndex.value === 0) {
    imageIndex.value = props.urls.length - 1
  } else {
    imageIndex.value -= 1
  }
  audio.play()
}

function handleKeyDown(e: KeyboardEvent) {
  e.preventDefault()
  if (e.key === 'ArrowRight' || e.key === 'Enter') {
    transitionForward()
  } else if (e.key === 'ArrowLeft') {
    transitionBackward()
  }
}

const touchstartX = ref<number>()
    
function onTouchEnd(e: TouchEvent) {
  const touchendX = e.changedTouches[0].screenX
  const thresholdPx = 25
  if (!touchstartX.value) {
    return
  }
  if ((touchendX + thresholdPx) < touchstartX.value) {
    transitionForward()
  } else if ((touchendX - thresholdPx) > touchstartX.value){
    transitionBackward()
  }
  touchstartX.value = undefined
}

const container = ref<HTMLElement>()
const containerType = ref('wide')

function debounce(callback: any, wait: number) {
  let timeoutId: any = null;
  return (...args: any[]) => {
    window.clearTimeout(timeoutId);
    timeoutId = window.setTimeout(() => {
      callback(...args);
    }, wait);
  };
}

function setContainerType() {
  if (container.value?.clientHeight && container.value?.clientWidth) {
    if (container.value?.clientHeight > container.value?.clientWidth) {
      containerType.value = 'tall'
    } else {
      containerType.value = 'wide'
    }
  }
}

const resizeObserver = ref(
  new ResizeObserver(debounce(setContainerType, 100))
)

watch(container, (newVal, oldVal) => {
  if (newVal) {
    if (!oldVal) {
      resizeObserver.value.observe(newVal)
    }
  } else {
    if (oldVal) {
      resizeObserver.value.unobserve(oldVal)
    }
  }
})

watch(imageIndex, (newVal) => {
  if (loadedImageCount.value !== props.urls.length) {
    if (loadedImageCount.value - newVal < 4) {
      props.urls
        .slice(loadedImageCount.value - 1, props.chunkSize)
        .forEach((url) => {
          const imageEl = new Image()
          imageEl.src = url
        })
      loadedImageCount.value += props.chunkSize
    }
  }
})


onMounted(async () => {
  window.addEventListener('keydown', handleKeyDown, false)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown, false)
})

</script>

<style lang="scss" scoped>

.image {
  object-fit: contain;
  &.wide {
    max-height: 100%;
  }
  &.tall {
    max-width: 100%;
  }
}

.forward-enter-active,
.backward-enter-active {
  transition: transform 0.15s linear;
}

.forward-enter-from {
  transform: translateX(500px);
}
.backward-enter-from {
  transform: translateX(-500px);
}

</style>
