<template>
  <div class="flex h-full justify-center">
    <div v-if="urls.length > 0" class="flex h-full w-full" ref="container">
      <Transition :key="groupIndex" :name="transition" appear>
        <div
          class="h-full flex justify-evenly cursor-pointer"
          @click="transitionForward()"
          @touchstart="touchstartX = $event.changedTouches[0].screenX"
          @touchend="onTouchEnd"
        >
          <img
            v-for="url in groups[groupIndex]"
            class="image h-full"
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

const groups = ref<string[][]>([])
const groupIndex = ref(0)
const transition = ref('forward')

const audio = new Audio(cursormove)
function transitionForward() {
  transition.value = 'forward'
  if (groupIndex.value === groups.value.length - 1) {
    groupIndex.value = 0
  } else {
    groupIndex.value += 1
  }
  audio.play()
}

function transitionBackward() {
  transition.value = 'backward'
  if (groupIndex.value === 0) {
    groupIndex.value = groups.value.length - 1
  } else {
    groupIndex.value -= 1
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

function calculateImageGroups(urls: string[]): Promise<string[][]> {
  type Image = { url: string, width: number }
  const images: Image[] = []
  if (!container.value) {
    return Promise.resolve([])
  }
  const { clientHeight, clientWidth } = container.value
  return new Promise(resolve => {
    urls.forEach((url) => {
      const imageEl = new Image()
      imageEl.onload = function() {
        const proportion = clientHeight / imageEl.height
        images.push({ url, width: imageEl.width * proportion })
        if (images.length === urls.length) {
          const containerSize = 5
          const sizes = [ ...Array(containerSize).keys() ].reverse().map(i => i + 1)
          const sizeToURLs: Record<number, Set<string>> = sizes.reduce((acc, size) => ({ ...acc, [size]: new Set([])}), {})
          const urlToSize: Record<string, number> = {}

          images.forEach(image => {
            const size = Math.min(
              Math.ceil((image.width / clientWidth) * containerSize),
              containerSize
            )
            sizeToURLs[size].add(image.url)
            urlToSize[image.url] = size
          })

          const remainingURLs = new Set(images.map(image => image.url))

          function createImageGroup(group: string[]) { // eslint-disable-line
            let chosenURL: string | undefined
            if (group.length === 0) {
              // choose first image
              chosenURL = remainingURLs.values().next().value
              if (chosenURL) {
                sizeToURLs[urlToSize[chosenURL]].delete(chosenURL)
              }
            } else {
              const groupSize = group.map(url => urlToSize[url]).reduce((a, b) => a + b, 0)
              const largestPossibleSize = containerSize - groupSize
              for (let size = largestPossibleSize; size > 0; size--) {
                const remainingURLsOfSize = sizeToURLs[size]
                const url = remainingURLsOfSize.values().next().value
                if (url) {
                  chosenURL = url
                  remainingURLsOfSize.delete(url)
                }
                if (chosenURL) break
              }
            }
            if (!chosenURL) {
              return group
            }
            remainingURLs.delete(chosenURL)
            return createImageGroup([...group, chosenURL])
          }

          function createImageGroups(groups: string[][]) { // eslint-disable-line
            if (!remainingURLs.size) {
              return groups
            }
            const newGroup = createImageGroup([])
            return createImageGroups([...groups, newGroup])
          }

          const groups = createImageGroups([])
          resolve(groups)
        }
      }
      imageEl.src = url
    })
  })
}

function debounce(callback: any, wait: number) {
  let timeoutId: any = null;
  return (...args: any[]) => {
    window.clearTimeout(timeoutId);
    timeoutId = window.setTimeout(() => {
      callback(...args);
    }, wait);
  };
}

const loadedURLIndex = ref(-1)

async function resetImageGroups() {
  groupIndex.value = 0
  const loadedURLs = props.urls.slice(0, loadedURLIndex.value + 1)
  groups.value = await calculateImageGroups(loadedURLs)
}
const resizeObserver = ref(
  new ResizeObserver(debounce(resetImageGroups, 100))
)

onMounted(async () => {
  window.addEventListener('keydown', handleKeyDown, false)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown, false)
})

watch(groupIndex, async (newVal) => {
  if (newVal >= groups.value.length - 3) {
    const urlsToLoad = props.urls.slice(loadedURLIndex.value + 1, loadedURLIndex.value + props.chunkSize + 1)
    const newGroups = await calculateImageGroups(urlsToLoad)
    groups.value = [...groups.value, ...newGroups]
    loadedURLIndex.value += props.chunkSize
  }
}, { immediate: true })

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
</script>

<style scoped>

@media screen and (max-width: 750px) {
  .image {
    max-height: 100%;
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
