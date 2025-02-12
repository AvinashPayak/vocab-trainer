<template>
  <div class="flex flex-col items-center justify-start px-4 mb-5">
    <div class="flex gap-2 items-center mb-4">
      <label class="font-bold">Range:</label>
      <input
        v-model.number="rangeStart"
        type="number"
        min="1"
        :max="words.length"
        class="border p-1 w-16 text-center"
      />
      <span>-</span>
      <input
        v-model.number="rangeEnd"
        type="number"
        min="1"
        :max="words.length"
        class="border p-1 w-16 text-center"
      />
      <button
        @click="validateRange"
        class="ml-2 bg-blue-500 text-white px-2 py-1 rounded"
      >
        Set
      </button>
    </div>
    <p v-if="completedTest" class="text-2xl font-bold">
      🎉 All words completed!
    </p>
    <div v-else>
      <h2 class="text-4xl font-bold mb-4">🔥 {{ streak }}</h2>
      <p class="text-lg font-bold mb-6">{{ currentWord.meaning }}</p>

      <div
        v-for="(option, index) in options"
        :key="index"
        @click="checkAnswer(option)"
        class="cursor-pointer bg-[#1a1a1a] px-6 py-3 rounded-lg text-sm mb-2 w-full max-w-md text-start hover:bg-gray-800"
        :class="{
          'bg-green-500 text-white':
            selectedOption === option && option === currentWord.word,
          'bg-red-500 text-white':
            selectedOption === option && option !== currentWord.word,
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
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const words = ref([]);
const currentWord = ref({ word: "", meaning: "" });
const options = ref([]);
const selectedOption = ref(null);
const optionLabels = ["A", "B", "C", "D", "E"];
const streak = ref(0);
const resetStreak = ref(false);
const rangeStart = ref(1);
const rangeEnd = ref(50);
const correctCounts = ref({});
const completedTest = ref(false);

const shuffleArray = (array) => {
  let shuffled = array.slice();
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
};

const validateRange = () => {
  if (rangeStart.value < 1) rangeStart.value = 1;
  if (rangeEnd.value > words.value.length) rangeEnd.value = words.value.length;
  if (rangeStart.value > rangeEnd.value) rangeEnd.value = rangeStart.value;
  resetTest();
};

const pickRandomWord = () => {
  if (resetStreak.value) {
    streak.value = 0;
    resetStreak.value = false;
  }
  selectedOption.value = null;

  const validWords = words.value
    .slice(rangeStart.value - 1, rangeEnd.value)
    .filter(
      (word) =>
        !correctCounts.value[word.word] || correctCounts.value[word.word] < 3
    );

  if (validWords.length === 0) {
    completedTest.value = true;
    return;
  }

  const randomIndex = Math.floor(Math.random() * validWords.length);
  currentWord.value = validWords[randomIndex];

  let incorrectOptions = words.value
    .filter((word) => word.word !== currentWord.value.word)
    .map((word) => word.word);

  incorrectOptions = shuffleArray(incorrectOptions).slice(0, 4);
  options.value = shuffleArray([currentWord.value.word, ...incorrectOptions]);
};

const checkAnswer = (option) => {
  selectedOption.value = option;
  if (option === currentWord.value.word) {
    streak.value += 1;
    correctCounts.value[currentWord.value.word] =
      (correctCounts.value[currentWord.value.word] || 0) + 1;
  } else {
    resetStreak.value = true;
    correctCounts.value[currentWord.value.word] = 0;
  }
};

const resetTest = () => {
  completedTest.value = false;
  pickRandomWord();
};

onMounted(async () => {
  const response = await fetch("/vocab.csv");
  const text = await response.text();
  words.value = text
    .split("\n")
    .filter((line) => line.trim())
    .map((line) => {
      const [word, meaning] = line.split(",");
      return { word: word.trim(), meaning: meaning.trim() };
    });

  validateRange();
  pickRandomWord();
});
</script>
