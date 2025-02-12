<template>
  <div class="container">
    <h2 class="text-[24px]" v-if="currentWord">{{ currentWord }}</h2>

    <button @click="toggleMeaning">
      {{ showMeaning ? "Hide Meaning" : "Show Meaning" }}
    </button>
    <button @click="pickRandomWord">Next Word</button>
    <p v-if="showMeaning">{{ currentMeaning }}</p>

    <p v-if="showMeaning && currentSentence" class="pt-5 text-orange-300">
      Eg: {{ currentSentence }}
    </p>
  </div>
</template>
<script setup>
import { ref, onMounted } from "vue";
import Papa from "papaparse";

const words = ref([]);
const currentWord = ref(null);
const currentMeaning = ref("");
const currentSentence = ref("");
const showMeaning = ref(false);
const toggleMeaning = () => {
  showMeaning.value = !showMeaning.value;
};

// Function to fetch and parse CSV
const loadCSV = async () => {
  try {
    const response = await fetch("/vocab.csv"); // Ensure the CSV file is in `public/`
    const csvText = await response.text();

    Papa.parse(csvText, {
      header: true,
      skipEmptyLines: true,
      complete: (result) => {
        words.value = result.data;
        pickRandomWord();
      },
    });
  } catch (error) {
    console.error("Error loading CSV:", error);
  }
};

// Function to pick a random word
const pickRandomWord = () => {
  if (words.value.length === 0) return;
  const randomIndex = Math.floor(Math.random() * words.value.length);
  currentWord.value = words.value[randomIndex].word;
  currentMeaning.value = words.value[randomIndex].meaning;
  currentSentence.value = words.value[randomIndex].sentence;
  showMeaning.value = false;
};

// Load CSV when component is mounted
onMounted(loadCSV);
</script>
<style scoped>
.container {
  text-align: center;
  margin-top: 20px;
}
button {
  margin: 10px;
  padding: 10px 15px;
  cursor: pointer;
  border: none;
  background: #42b983;
  color: white;
  border-radius: 5px;
}
button:hover {
  background: #369f7f;
}
</style>
