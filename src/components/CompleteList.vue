<template>
    <div class="relative pb-20">  
      <!-- Scrollable Container -->
      <div class="max-h-1/3 overflow-y-scroll border border-[#4a4a4a] rounded-md">
        <table class="min-w-full border-collapse">
          <thead class="bg-[#4a4a4a]">
            <tr>
              <th class="border p-2">Word</th>
              <th class="border p-2">Meaning</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(word, index) in words" :key="index" class="hover:bg-gray-100 text-[12px]">
              <td class="border p-2">{{ word.word }}</td>
              <td class="border p-2">{{ word.meaning }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  
  // Store words data
  const words = ref([]);
  
  // Function to fetch and parse CSV
  const fetchCSV = async () => {
    try {
      const response = await fetch("/vocab.csv"); // ✅ Fetch CSV from public folder
      const text = await response.text();
      const rows = text.split("\n").slice(1); // Remove header row
  
      words.value = rows.map((row) => {
        const [word, meaning] = row.split(",");
        return { word: word.trim(), meaning: meaning?.trim() || "N/A" };
      });
    } catch (error) {
      console.error("Error loading CSV:", error);
    }
  };
  
  // Fetch CSV on mount
  onMounted(fetchCSV);
  </script>
  
  <style scoped>
  /* Ensure proper spacing */
  </style>
  