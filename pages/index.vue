<template>
  <!-- Animated Earth Background Section -->
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-green-50 overflow-hidden">
    <!-- Floating Earth Animation -->
    <div class="absolute top-1/4 left-1/4 w-32 h-32 rounded-full bg-blue-200 opacity-20 animate-float1"></div>
    <div class="absolute top-1/3 right-1/4 w-48 h-48 rounded-full bg-green-200 opacity-20 animate-float2"></div>
    <div class="absolute bottom-1/4 right-1/3 w-40 h-40 rounded-full bg-teal-200 opacity-20 animate-float3"></div>
    
    <!-- Hero Section -->
    <div class="hero min-h-screen relative z-10">
      <div class="hero-content flex-col lg:flex-row gap-12 lg:gap-24 px-4 sm:px-8">
        <!-- Earth Avatar with Bounce Animation -->
        <div class="avatar animate-bounce">
          <div class="w-64 h-64 sm:w-80 sm:h-80 rounded-full border-8 border-white shadow-xl overflow-hidden">
            <img src="/public/assets/icon.png" class="w-full h-full object-cover" />
          </div>
        </div>
        
        <!-- Content with Fun Typography -->
        <div class="text-center lg:text-left max-w-2xl">
          <h1 class="text-4xl sm:text-5xl md:text-6xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-green-600 to-blue-600 mb-6">
            Save Our Planet!
          </h1>
          
          <div class="space-y-4">
            <p class="text-lg sm:text-xl text-gray-700 leading-relaxed">
              🌍 Jelajahi dan pelajari cara menjaga bumi dengan cara yang <span class="font-bold text-green-600">menyenangkan</span>! 
            </p>
            
            <p class="text-lg sm:text-xl text-gray-700 leading-relaxed">
              📚 Cek kondisi cuaca dan kualitas udara di sekitarmu,<span class="font-bold text-blue-600">kuis interaktif</span>, dan mainkan <span class="font-bold text-purple-600">game seru</span> untuk membersihkan sampah!
            </p>
            
            <p class="text-xl sm:text-2xl font-medium text-green-700 mt-6">
              Mulai dari langkah kecil, bersama kita bisa membuat perubahan besar untuk bumi yang lebih hijau! 🌱
            </p>
          </div>
          
        </div>
      </div>
    </div>
  </div>

  <!-- Activities Section -->
  <div class="py-16 px-4 sm:px-8 bg-white">
    <div class="max-w-6xl mx-auto">
      <h2 class="text-3xl sm:text-4xl font-bold text-center mb-12 text-transparent bg-clip-text bg-gradient-to-r from-green-600 to-blue-600">
        Ayo Lakukan Aksi! ✨
      </h2>
      
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
        <div v-for="project in projects" :key="project.id" 
             class="card bg-base-100 w-full shadow-xl hover:shadow-2xl transition-all duration-300 border-2 border-gray-100 rounded-2xl overflow-hidden group">
          <figure class="relative overflow-hidden h-48">
            <img :src="project.image" :alt="project.name" 
                 class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500" />
            <div class="absolute inset-0 bg-gradient-to-t from-black/60 to-transparent"></div>
          </figure>
          <div class="card-body items-center text-center p-6">
            <h2 class="card-title text-2xl font-bold text-gray-800">
              {{ project.name }}
              <span v-if="project.name === 'Cuaca & Udara'" class="text-blue-500">📰</span>
              <span v-else-if="project.name === 'Game'" class="text-green-500">🎮</span>
              <span v-else class="text-yellow-500">❓</span>
            </h2>
            <p class="text-gray-600">{{ project.deskripsi }}</p>
            <div class="card-actions mt-4">
              <a :href="project.subject" 
                 class="btn btn-primary px-8 rounded-full bg-gradient-to-r from-green-500 to-blue-500 border-0 hover:from-green-600 hover:to-blue-600 transform hover:-translate-y-1 transition-all duration-300">
                Jelajahi
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Fun Facts Section -->
  <div class="py-16 px-4 sm:px-8 bg-gradient-to-br from-green-50 to-blue-50">
    <div class="max-w-4xl mx-auto text-center">
      <h2 class="text-3xl sm:text-4xl font-bold mb-8 text-green-700">
        Tahukah Kamu? 🤔
      </h2>
      
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <div class="bg-white p-6 rounded-xl shadow-md hover:shadow-lg transition-shadow duration-300">
          <div class="text-4xl mb-4">🌳</div>
          <h3 class="text-xl font-bold text-green-600 mb-2">1 Pohon</h3>
          <p class="text-gray-700">Dapat menghasilkan oksigen untuk 4 orang setiap harinya!</p>
        </div>
        
        <div class="bg-white p-6 rounded-xl shadow-md hover:shadow-lg transition-shadow duration-300">
          <div class="text-4xl mb-4">♻️</div>
          <h3 class="text-xl font-bold text-green-600 mb-2">Daur Ulang</h3>
          <p class="text-gray-700">1 botol plastik yang didaur ulang menghemat energi cukup untuk menyalakan lampu 60W selama 6 jam!</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
html {
  scroll-behavior: smooth;
}

/* Floating animations */
@keyframes float1 {
  0%, 100% { transform: translate(0, 0) rotate(0deg); }
  50% { transform: translate(20px, 30px) rotate(5deg); }
}

@keyframes float2 {
  0%, 100% { transform: translate(0, 0) rotate(0deg); }
  50% { transform: translate(-25px, 20px) rotate(-5deg); }
}

@keyframes float3 {
  0%, 100% { transform: translate(0, 0) rotate(0deg); }
  50% { transform: translate(15px, -20px) rotate(3deg); }
}

.animate-float1 {
  animation: float1 8s ease-in-out infinite;
}

.animate-float2 {
  animation: float2 10s ease-in-out infinite;
}

.animate-float3 {
  animation: float3 12s ease-in-out infinite;
}

/* Bounce animation for earth */
@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-20px); }
}

.animate-bounce {
  animation: bounce 3s ease-in-out infinite;
}

/* Gradient text animation */
.gradient-text {
  background-size: 200% auto;
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: gradient 3s linear infinite;
}

@keyframes gradient {
  0% { background-position: 0% center; }
  100% { background-position: 200% center; }
}
</style>

<script setup>
// Data projects with enhanced properties
const db_projects = ref([
  {
    id: 1,
    name: 'Cuaca & Udara',
    deskripsi: 'Lihat kondisi cuaca dan kualitas udara di lokasimu',
    subject: '/kondisi',
    image: '/assets/3.png'
  },
  {
    id: 2,
    name: 'Game',
    deskripsi: 'Mainkan Game seru yang bertema bersihkan bumi',
    subject: '/game',
    image: '/assets/2.png'  
  },
  {
    id: 3,
    name: 'Kuis',
    deskripsi: 'Jawab beberapa pertanyaan terkait alam dan bumi',
    subject: '/kuis',
    image: '/assets/4.png'
  },
]);

const projects = ref([...db_projects.value]);
</script>