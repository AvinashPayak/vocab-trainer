<template>
  <component :is="currentComponent.name" />
  <NavBar :currentTab="currentComponent"  @tab-changed="updateTab" />
</template>
<script setup>
import RandomWord from './components/RandomWord.vue'
import NavBar from './components/NavBar.vue'
import CompleteList from './components/CompleteList.vue';
import Test from './components/Test.vue';
import GuessWord from './components/GuessWord.vue';

import { shallowRef } from 'vue';

const componentList = [
  {
    id : 1,
    name: RandomWord
  },
  {
    id: 2,
    name: CompleteList
  },
  {
    id: 3,
    name: Test
  },
  {
    id: 4,
    name: GuessWord
  }
]

const currentComponent = shallowRef(componentList[0])

const updateTab = (tabId) => {
  const newComponent = componentList.find((comp) => comp.id === tabId);
  if (newComponent) {
    currentComponent.value = newComponent;
  }
};
</script>
<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
