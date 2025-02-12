<template>
  <div class="w-screen fixed left-0 bottom-0 py-4 bg-[#1a1a1a] px-4">
    <ul class="flex text-[16px] overflow-x-auto whitespace-nowrap scrollbar-hidden justify-between">
        <li class="px-2" :class="props.currentTab.id === item.id? 'bg-[#4a4a4a] br-4':''" v-for="item in list" :key="item.id"  @click="changeTab(item)">{{ item.title }}</li>
    </ul>
  </div>
</template>
<script setup>
import { ref } from "vue";

const isOpen = ref(false);
const activeTab = ref("random");
const list = [
  {
    id: 1,
    title: 'Learn'
  },
  {
    id: 2,
    title: 'Complete List'
  },
  {
    id: 3,
    title: 'Test?'
  },
  {
    id: 4,
    title: 'Guess Word'
  }
]

const props = defineProps({
  currentTab: {
    type: Object,
    default: () => ({ id: null })
  },
})

const emit = defineEmits(["tab-changed"]);
const changeTab = (item) => {
  emit("tab-changed", item.id); // ✅ Emit event with selected tab ID
};
</script>

<style scoped>
button {
  cursor: pointer;
}
</style>
