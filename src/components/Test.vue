<template>
  <div class="flex flex-col items-center justify-start px-4">
    <h2 class="text-4xl font-bold mb-4">🔥 {{ streak }}</h2>
    <h1 class="text-3xl font-bold mb-6">{{ currentWord.word }}</h1>

    <div
      v-for="(option, index) in options"
      :key="index"
      @click="checkAnswer(option)"
      class="cursor-pointer bg-[#1a1a1a] px-6 py-3 rounded-lg text-lg mb-2 w-full max-w-md text-center hover:bg-gray-800"
      :class="{
        'bg-green-500 text-white':
          selectedOption === option && option === currentWord.meaning,
        'bg-red-500 text-white':
          selectedOption === option && option !== currentWord.meaning,
      }"
    >
      <span class="font-bold">{{ optionLabels[index] }}.</span> {{ option }}
    </div>

    <button
      :disabled="!selectedOption"
      @click="pickRandomWord"
      class="mt-4 bg-blue-500 text-white px-4 py-2 rounded"
      :class="{
        'bg-blue-500 text-white cursor-pointer': selectedOption,
        'bg-gray-200 text-gray-700 cursor-not-allowed': !selectedOption,
      }"
    >
      Next Word
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const words = ref([]);
const currentWord = ref({ word: "", meaning: "" });
const options = ref([]);
const selectedOption = ref(null);
const optionLabels = ["A", "B", "C", "D", "E"];
const streak = ref(0); // Streak counter

const shuffleArray = (array) => {
  let shuffled = array.slice();
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
};

const pickRandomWord = () => {
  selectedOption.value = null;

  // Pick a random word
  const randomIndex = Math.floor(Math.random() * words.value.length);
  currentWord.value = words.value[randomIndex];

  // Select incorrect meanings
  let incorrectOptions = words.value
    .filter((word) => word.meaning !== currentWord.value.meaning)
    .map((word) => word.meaning);

  incorrectOptions = shuffleArray(incorrectOptions).slice(0, 4); // Pick 4 random wrong options

  // Shuffle the correct and incorrect meanings
  options.value = shuffleArray([
    currentWord.value.meaning,
    ...incorrectOptions,
  ]);
};

const checkAnswer = (option) => {
  selectedOption.value = option;
  if (option === currentWord.value.meaning) {
    streak.value += 1; // Increase streak for correct answer
  } else {
    streak.value = 0; // Reset streak for wrong answer
  }
};

onMounted(async () => {
  // Fetch the CSV file from the public directory
  const response = await fetch("/vocab.csv");
  const text = await response.text();
  words.value = text
    .split("\n")
    .filter((line) => line.trim()) // Remove empty lines
    .map((line) => {
      const [word, meaning] = line.split(",");
      return { word: word.trim(), meaning: meaning.trim() };
    });

  pickRandomWord();
});
</script>
