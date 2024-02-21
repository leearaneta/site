<script setup lang="ts">
import { onMounted, ref } from 'vue';
import VerticalSelection from './VerticalSelection.vue';
import TheProfile from './TheProfile.vue';
import WorkExperience from './WorkExperience.vue'
import TheSkills from './TheSkills.vue';
import TheProjects from './TheProjects.vue';
import TheCallie from './TheCallie.vue';

const width = ref(window.innerWidth)

onMounted(() => {
  window.onresize = () => {
    width.value = window.innerWidth
  }
})

const selected = ref('Work Experience')
</script>

<template>
  <div class="grid">
    <Transition name="left" appear>
      <div class="profile">
        <div class="h-full section profile-padding flex">
          <TheProfile />
        </div>
      </div>
    </Transition>
    <Transition name="down" appear>
      <div v-if="width >= 750" class="menu">
        <div class="h-full section py-4 px-2">
          <VerticalSelection
            :options="['Work Experience', 'Skills', 'Projects', 'Callie']"
            @select="selected = $event"
          />
        </div>
      </div>
    </Transition>
    <Transition name="left" appear>
      <div v-if="width >= 750" class="body">
        <div class="h-full section py-4 pl-10 pr-28 flex flex-col">
          <WorkExperience v-if="selected === 'Work Experience'" />
          <TheSkills v-if="selected === 'Skills'" />
          <TheProjects v-if="selected === 'Projects'" />
          <TheCallie v-if="selected === 'Callie'" />
        </div>
      </div>
    </Transition>
    <Transition name="right" appear>
      <div v-if="width >= 750" class="time">
        <div class="h-full section p-4"> what goes here? </div>
      </div>
    </Transition>
    <Transition name="right" appear>
      <div v-if="width >= 750" class="location">
        <div class="h-full section p-4 flex items-center"> Brooklyn, NY </div>
      </div>
    </Transition>
    <Transition name="up" appear>
      <div v-if="width < 750" class="scrollable">
        <div class="h-full section flex flex-col">
          <div class="overflow-scroll w-full p-4">
            <div class="mb-4">
              <div class="mb-2"> Work Experience </div>
              <div> <WorkExperience /> </div>
            </div>
            <div class="mb-4">
              <div class="mb-2"> Skills </div>
              <div> <TheSkills /> </div>
            </div>
            <div class="mb-4">
              <div class="mb-2"> Projects </div>
              <div> <TheProjects /> </div>
            </div>
            <div class="h-full">
              <div class="mb-4"> Callie </div>
              <div class="h-full pb-2"> <TheCallie /> </div>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>

.grid {
  height: 100%;
}

@media screen and (min-width: 1000px) {
  .grid {
    max-width: 1000px;
  }
}

@media screen and (min-width: 750px) {
  .grid {
    margin: auto;
    grid-template-columns: 1fr 30rem 5rem 15rem;
    grid-template-rows: 2.5rem 12rem 1fr 4rem 8rem 1.5rem 3rem;
  }

  .profile {
    grid-column: 1 / 4;
    grid-row: 2 / 3;
  }

  .profile-padding {
    padding-top: 1rem;
    padding-bottom: 1rem;
    padding-left: 2.5rem;
    padding-right: 5rem;
  }

  .body {
    grid-column: 1 / 4;
    grid-row: 3 / 7;
  }

  .menu {
    grid-column: 3 / 5;
    grid-row: 1 / 4;
  }

  .time {
    grid-column: 3 / 5;
    grid-row: 5 / 6;
  }

  .location {
    grid-column: 3 / 5;
    grid-row: 6 / 8;
  }
}

@media screen and (max-width: 750px) {
  .grid {
    grid-template-rows: 25vh 75vh;
    overflow: hidden;
  }

  .profile {
    grid-row: 1;
  }

  .scrollable {
    grid-row: 2;
  }

  .profile-padding {
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

}

.menu, .time, .location {
  z-index: 1;
}

.left-enter-active,
.down-enter-active,
.right-enter-active,
.up-enter-active {
  transition: transform 0.4s ease;
}

.left-enter-from {
  transform: translateX(1000px)
}

.down-enter-from {
  transform: translateY(-1000px)
}

.right-enter-from {
  transform: translateX(-1000px)
}

.up-enter-from {
  transform: translateY(1000px)
}

</style>