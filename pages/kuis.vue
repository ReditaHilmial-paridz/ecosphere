<template>
    <!-- Background with nature animation -->
    <div class="min-h-screen bg-gradient-to-b from-blue-50 to-green-50 py-6 px-4 sm:px-6 lg:px-8">
      <a href="/" class="fixed bottom-5 right-5 btn btn-circle btn-success shadow-lg">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" 
          viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" 
            stroke-linejoin="round" stroke-width="2" 
            d="M9 5l7 7-7 7" />
        </svg>
    </a>
        <!-- Quiz Header -->
      <div class="max-w-4xl mx-auto text-center mb-8 sm:mb-10 animate-fade-in">
        <h1 class="text-3xl sm:text-4xl md:text-5xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-green-600 to-blue-600 mb-3 sm:mb-4">
          Kuis Penyelamat Bumi 🌱
        </h1>
        <p class="text-lg sm:text-xl text-gray-700">
          Uji pengetahuanmu tentang lingkungan!
        </p>
        <div class="mt-4 sm:mt-6 flex justify-center">
          <div class="badge badge-lg badge-primary gap-2 animate-pulse">
            ✨ jawab pertanyaan dibawah ini untuk jadi pemenang!
          </div>
        </div>
      </div>
  
    <!-- Quiz Container -->
        <div class="max-w-2xl mx-auto bg-white rounded-2xl sm:rounded-3xl shadow-lg sm:shadow-xl overflow-hidden border-2 sm:border-4 border-green-200">
            <!-- Question Area -->
            <div class="p-4 sm:p-6 md:p-8">
                <!-- Question Number -->
                <div class="flex justify-between items-center mb-4 sm:mb-6">
                <span class="text-base sm:text-lg font-medium text-gray-500">
                    Pertanyaan {{ currentQuestionIndex + 1 }} dari {{ questions.length }}
                </span>
                <span class="text-base sm:text-lg font-bold text-green-600">
                    💎 {{ score }} Poin
                </span>
            </div>
  
          <!-- Question -->

            <span v-if="currentQuestion.image" class="block mt-3 sm:mt-4">
              <img :src="currentQuestion.image" class="max-h-32 sm:max-h-40 mx-auto rounded-lg" alt="Ilustrasi pertanyaan">
              <h2 class="text-xl sm:text-2xl md:text-3xl font-bold text-gray-800 mb-6 sm:mb-8 leading-tight">
                {{ currentQuestion.question }}
              </h2>
            </span>
  
          <!-- Answer Options -->
          <div class="grid grid-cols-1 gap-2 sm:gap-3 mb-6 sm:mb-8">
            <button 
                v-for="(option, index) in currentQuestion.options" 
                :key="index"
                @click="checkAnswer(option.isCorrect, index)"
                class="btn btn-outline justify-start py-3 sm:py-4 px-4 sm:px-6 text-base sm:text-lg rounded-lg sm:rounded-xl transition-all my-1 sm:my-1.5 whitespace-normal text-left"
                :class="{
                'hover:bg-green-100 hover:border-green-400 hover:scale-[1.02]': !selectedAnswer,
                'bg-green-100 border-green-400': selectedAnswer && option.isCorrect,
                'bg-red-100 border-red-400': selectedAnswer && !option.isCorrect && selectedOption === index,
                'opacity-70': selectedAnswer && !option.isCorrect
                }"
                :disabled="selectedAnswer">
                <span class="mr-2 sm:mr-3 text-lg sm:text-xl">{{ ['A', 'B', 'C', 'D'][index] }}.</span>
                <span class="flex-1">{{ option.text }}</span>
                <span v-if="selectedAnswer && option.isCorrect" class="ml-2 text-green-600">✅</span>
                <span v-if="selectedAnswer && !option.isCorrect && selectedOption === index" class="ml-2 text-red-600">❌</span>
            </button>
          </div>
  
          <!-- Feedback & Next Button -->
          <div v-if="selectedAnswer" class="animate-fade-in">
            <div class="alert mb-4 sm:mb-6" :class="{
              'alert-success': isCorrect,
              'alert-error': !isCorrect
            }">
              <div>
                <span v-if="isCorrect">🎉 Yeay! Jawabanmu benar!</span>
                <span v-else>😢 Aduh, jawabanmu kurang tepat...</span>
                <div class="text-xs sm:text-sm mt-1">{{ currentQuestion.explanation }}</div>
              </div>
            </div>
  
            <button 
              @click="nextQuestion"
              class="btn btn-primary w-full py-3 sm:py-4 text-base sm:text-lg rounded-lg sm:rounded-xl bg-gradient-to-r from-green-500 to-blue-500 border-0 hover:from-green-600 hover:to-blue-600">
              {{ isLastQuestion ? 'Lihat Hasil Akhir' : 'Pertanyaan Berikutnya' }}
              <span class="ml-2">👉</span>
            </button>
          </div>
        </div>
      </div>
  
      <!-- Eco Hero Character -->
      <div v-if="showCharacter" class="fixed bottom-0 right-0 mr-2 sm:mr-4 mb-2 sm:mb-4 animate-bounce">
        <img src="/assets/eco-hero.png" class="w-24 h-24 sm:w-32 sm:h-32" alt="Eco Hero">
      </div>
  
      <!-- Results Popup -->
      <div v-if="showResults" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-2 sm:p-4">
        <div class="bg-white rounded-xl sm:rounded-2xl md:rounded-3xl overflow-hidden shadow-lg sm:shadow-xl w-full max-w-xs sm:max-w-sm md:max-w-md mx-2 animate-pop-in">
          <!-- Header with confetti -->
          <div class="relative bg-gradient-to-r from-green-400 to-blue-500 py-4 sm:py-6 text-center">
            <div class="absolute top-0 left-0 right-0 h-1 bg-yellow-400"></div>
            <h2 class="text-2xl sm:text-3xl font-bold text-white">Hasil Akhir!</h2>
            <div class="confetti"></div>
            <div class="confetti"></div>
            <div class="confetti"></div>
          </div>
          
          <!-- Body Content -->
          <div class="p-4 sm:p-6 text-center">
            <!-- Animated Trophy -->
            <div class="relative inline-block mb-4 sm:mb-6">
              <img src="/assets/trophy.png" class="w-24 h-24 sm:w-32 sm:h-32 animate-bounce" alt="Trophy">
              <span class="absolute -top-1 -right-1 sm:-top-2 sm:-right-2 bg-yellow-400 text-white rounded-full w-8 h-8 sm:w-10 sm:h-10 flex items-center justify-center text-lg sm:text-xl font-bold animate-ping">
                !
              </span>
            </div>
            
            <!-- Score Display -->
            <div class="text-4xl sm:text-5xl font-bold mb-1 sm:mb-2 text-transparent bg-clip-text bg-gradient-to-r from-green-600 to-blue-600">
              {{ score }} Poin!
            </div>
            
            <!-- Performance Message -->
            <div class="text-lg sm:text-xl font-medium mb-4 sm:mb-6">
              <span v-if="score >= 300">🎉 Wow! Kamu Juara Lingkungan!</span>
              <span v-else-if="score >= 200">👍 Kerja Bagus! Kamu Peduli Bumi!</span>
              <span v-else>💪 Terus Belajar ya! Bumi Butuh Kamu!</span>
            </div>
            

            
            
            <!-- Action Buttons -->
            <div class="grid grid-cols-2 gap-3 sm:gap-4">
              <button @click="restartQuiz" class="btn btn-outline btn-primary btn-sm sm:btn-md rounded-full">
                Main Lagi
              </button>
              <button @click="goToEcoActions" class="btn btn-primary btn-sm sm:btn-md rounded-full bg-gradient-to-r from-green-500 to-blue-500 border-0">
                Beranda
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <style>
  @keyframes fade-in {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
  }
  
  @keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-15px); }
  }
  
  @keyframes pop-in {
    0% { transform: scale(0.8); opacity: 0; }
    80% { transform: scale(1.05); }
    100% { transform: scale(1); opacity: 1; }
  }
  
  @keyframes rotate-y {
    0% { transform: rotateY(0deg); }
    100% { transform: rotateY(360deg); }
  }
  
  @keyframes confetti-fall {
    0% { transform: translateY(-50px) rotate(0deg); opacity: 1; }
    100% { transform: translateY(80px) rotate(360deg); opacity: 0; }
  }
  
  .animate-fade-in {
    animation: fade-in 0.4s ease-out;
  }
  
  .animate-bounce {
    animation: bounce 1.8s infinite;
  }
  
  .animate-pop-in {
    animation: pop-in 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
  }
  
  .animate-rotate-y {
    animation: rotate-y 0.8s ease-in-out;
  }
  
  .btn-outline {
  border-width: 1.5px;
  background-color: white;
  margin: 0.25rem 0;
  word-break: break-word;
  white-space: normal;
  text-align: left;
  display: flex;
  align-items: center;
}

@media (max-width: 640px) {
  .btn {
    padding-left: 0.75rem;
    padding-right: 0.75rem;
  }
}
  
  /* Confetti Effect */
  .confetti {
    position: absolute;
    width: 8px;
    height: 8px;
    background-color: #f0f;
    opacity: 0.6;
    animation: confetti-fall 4s linear infinite;
  }
  
  .confetti:nth-child(1) {
    left: 10%;
    background-color: #f0f;
    animation-delay: 0s;
  }
  
  .confetti:nth-child(2) {
    left: 30%;
    background-color: #0ff;
    animation-delay: 1s;
  }
  
  .confetti:nth-child(3) {
    left: 60%;
    background-color: #ff0;
    animation-delay: 1.5s;
  }
  
  /* Badge container */
  .badge-container {
    transition: all 0.3s;
  }
  
  .badge-container:hover {
    transform: scale(1.15) rotate(12deg);
  }
  

  
  /* Responsive adjustments */
  @media (max-width: 640px) {
    .btn {
      padding-left: 0.75rem;
      padding-right: 0.75rem;
    }
  }
  </style>
  
  <script setup>
  import { ref, computed } from 'vue';
  
  const questions = [
    {
      question: "Manakah yang termasuk sumber polusi udara?",
      options: [
        { text: "Oksigen dari pohon", isCorrect: false },
        { text: "Asap pabrik dan kendaraan", isCorrect: true },
        { text: "Nektar bunga", isCorrect: false },
        { text: "Para pejalan kaki", isCorrect: false }
      ],
      explanation: "Asap dari pabrik dan mobil mengandung zat berbahaya yang mencemari udara.",
      image: "/assets/trash-polution.png"
    },
    {
      question: "Apa yang bisa kamu lakukan untuk menghemat listrik di rumah?",
      options: [
        { text: "Menyalakan lampu terus", isCorrect: false },
        { text: "Membiarkan AC menyala", isCorrect: false },
        { text: "Mematikan TV saat selesai", isCorrect: true },
        { text: "Menyalakan semua kipas", isCorrect: false }
      ],
      explanation: "Jika TV dibiarkan menyala terus-menerus, listrik akan terus terpakai, padahal tidak ada yang menonton. Ini membuat energi terbuang percuma.",
      image: "/assets/save-energy.png"
    },
    {
      question: "Apa manfaat dari menanam pohon?",
      options: [
        { text: "Menghasilkan oksigen", isCorrect: true },
        { text: "Membuat rumah lebih gelap", isCorrect: false },
        { text: "Menghabiskan air tanah", isCorrect: false },
        { text: "Menarik banyak nyamuk", isCorrect: false }
      ],
      explanation: "Pohon menghasilkan oksigen yang kita butuhkan untuk bernapas dan menyerap karbon dioksida yang berbahaya.",
      image: "/assets/plant-tree.png"
    }
  ];
  
  const currentQuestionIndex = ref(0);
  const score = ref(0);
  const selectedAnswer = ref(false);
  const selectedOption = ref(null);
  const isCorrect = ref(false);
  const showCharacter = ref(false);
  const showResults = ref(false);
  
  const currentQuestion = computed(() => questions[currentQuestionIndex.value]);
  const isLastQuestion = computed(() => currentQuestionIndex.value === questions.length - 1);
  
  const badges = [
    {
      name: "Pemula Hijau",
      image: "/assets/badges/beginner.png",
      scoreRequired: 100,
      tooltip: "Langkah pertama menjadi pahlawan bumi!"
    },
    {
      name: "Penjaga Lingkungan",
      image: "/assets/badges/guardian.png",
      scoreRequired: 200,
      tooltip: "Kamu sudah mulai peduli lingkungan!"
    },
    {
      name: "Juara Ekologi",
      image: "/assets/badges/champion.png",
      scoreRequired: 300,
      tooltip: "Kamu ahli dalam pengetahuan lingkungan!"
    }
  ];
  
  const earnedBadges = computed(() => {
    return badges.filter(badge => score.value >= badge.scoreRequired);
  });
  
  function checkAnswer(correct, optionIndex) {
    selectedAnswer.value = true;
    selectedOption.value = optionIndex;
    isCorrect.value = correct;
    
    if (correct) {
      score.value += 100;
      showCharacter.value = true;
      setTimeout(() => showCharacter.value = false, 3000);
    }
  }
  
  function nextQuestion() {
    if (isLastQuestion.value) {
      showResults.value = true;
    } else {
      currentQuestionIndex.value++;
      selectedAnswer.value = false;
      selectedOption.value = null;
    }
  }
  
  function restartQuiz() {
    showResults.value = false;
    currentQuestionIndex.value = 0;
    score.value = 0;
    selectedAnswer.value = false;
    selectedOption.value = null;
  }
  
  function goToEcoActions() {
  // Arahkan ke halaman utama
  window.location.href = "/";
  }

  </script>