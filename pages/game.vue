<template>
    <div class="games-page">
 
      <!-- Header dengan judul animasi -->
      <header class="game-header">
        <h1 class="title">🎮 Game Lingkungan Seru</h1>
        <p class="subtitle">Belajar menjaga lingkungan sambil bermain!</p>
      </header>
  
      <!-- Container untuk game cards -->
      <div class="games-container">
        
        <!-- Game 1 -->
        <div class="game-card" @click="openGame('https://poki.com/id/g/ecosystem')">
          <div class="game-badge" style="background-color: #2ecc71;">Edukasi!</div>
          <div class="game-image" style="background-image: url('https://cdn.pixabay.com/photo/2020/06/25/09/34/eco-5338714_1280.jpg');"></div>
          <div class="game-content">
            <h3>Petualangan Ekosistem</h3>
            <p>Jelajahi berbagai ekosistem dan pelajari keseimbangan alam!</p>
            <div class="game-stats">
              <span>⭐ 4.7/5</span>
              <span>🌿 Edukasi</span>
            </div>
          </div>
          <button class="play-button">Mainkan Sekarang</button>
        </div>
  
        <!-- Game 2 -->
        <div class="game-card" @click="openGame('https://poki.com/id/g/recycle-master')">
          <div class="game-badge" style="background-color: #3498db;">Keren!</div>
          <div class="game-image" style="background-image: url('https://cdn.pixabay.com/photo/2017/09/08/18/20/recycling-2729608_1280.jpg');"></div>
          <div class="game-content">
            <h3>Ahli Daur Ulang</h3>
            <p>Jadilah master daur ulang dan selamatkan bumi!</p>
            <div class="game-stats">
              <span>⭐ 4.5/5</span>
              <span>♻️ Aksi</span>
            </div>
          </div>
          <button class="play-button">Mainkan Sekarang</button>
        </div>
  
        <!-- Game 3 -->
        <div class="game-card" @click="openGame('https://poki.com/id/g/ocean-cleanup')">
          <div class="game-badge" style="background-color: #f39c12;">Seru!</div>
          <div class="game-image" style="background-image: url('https://cdn.pixabay.com/photo/2020/04/12/01/58/ocean-5032386_1280.jpg');"></div>
          <div class="game-content">
            <h3>Pembersih Samudra</h3>
            <p>Bersihkan laut dari sampah dan selamatkan kehidupan laut!</p>
            <div class="game-stats">
              <span>⭐ 4.8/5</span>
              <span>🐠 Petualangan</span>
            </div>
          </div>
          <button class="play-button">Mainkan Sekarang</button>
        </div>
        <a href="/" class="fixed bottom-5 right-5 btn btn-circle btn-success shadow-lg">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" 
            viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" 
                stroke-linejoin="round" stroke-width="2" 
                d="M9 5l7 7-7 7" />
            </svg>
        </a>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    methods: {
      openGame(url) {
        window.open(url, '_blank');
      }
    }
  }
  </script>
  
  <style scoped>
  /* Base Styles */
  .games-page {
    min-height: 100vh;
    background: linear-gradient(to bottom, #f5f7fa, #e4e8eb);
    padding: 20px;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }
  
  /* Header Styles */
  .game-header {
    text-align: center;
    margin-bottom: 40px;
    animation: fadeIn 1s ease-in-out;
  }
  
  .title {
    font-size: clamp(2rem, 6vw, 3rem);
    color: #2c3e50;
    margin-bottom: 10px;
    background: linear-gradient(90deg, #4b6cb7, #182848);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    display: inline-block;
  }
  
  .subtitle {
    font-size: clamp(1rem, 3vw, 1.3rem);
    color: #4a4e69;
    margin-top: 0;
  }
  
  /* Games Container */
  .games-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 30px;
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
  }
  
  /* Game Card Styles */
  .game-card {
    background: white;
    border-radius: 15px;
    overflow: hidden;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease;
    cursor: pointer;
    position: relative;
    display: flex;
    flex-direction: column;
    border-left: 6px solid #4b6cb7;
  }
  
  .game-card:hover {
    transform: translateY(-10px);
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  }
  
  .game-badge {
    position: absolute;
    top: 15px;
    right: 15px;
    color: white;
    padding: 5px 10px;
    border-radius: 20px;
    font-size: 0.8rem;
    font-weight: bold;
    z-index: 2;
  }
  
  .game-image {
    height: 200px;
    background-size: cover;
    background-position: center;
    position: relative;
  }
  
  .game-image::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 50%;
    background: linear-gradient(to top, rgba(255,255,255,0.9) 0%, transparent 100%);
  }
  
  .game-content {
    padding: 20px;
    flex-grow: 1;
  }
  
  .game-content h3 {
    margin: 0 0 10px 0;
    color: #2c3e50;
    font-size: 1.5rem;
  }
  
  .game-content p {
    color: #4a4e69;
    margin: 0 0 15px 0;
    line-height: 1.5;
  }
  
  .game-stats {
    display: flex;
    justify-content: space-between;
    margin-top: 15px;
    color: #4b6cb7;
    font-size: 0.9rem;
  }
  
  .play-button {
    background: linear-gradient(135deg, #4b6cb7, #182848);
    color: white;
    border: none;
    padding: 15px;
    font-size: 1.1rem;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s;
    width: 100%;
    border-radius: 0 0 15px 15px;
  }
  
  .play-button:hover {
    background: linear-gradient(135deg, #3a56a8, #101d38);
  }
  /* Animations */
  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(-20px); }
    to { opacity: 1; transform: translateY(0); }
  }
  
  /* Responsive Adjustments */
  @media (max-width: 768px) {
    .games-container {
      grid-template-columns: 1fr;
      max-width: 500px;
    }
    
    .game-image {
      height: 180px;
    }
  }
  
  @media (max-width: 480px) {
    .title {
      font-size: 1.8rem;
    }
    
    .game-card {
      border-radius: 15px;
    }
    
    .game-content h3 {
      font-size: 1.3rem;
    }
    
    .play-button {
      padding: 12px;
    }
  }
  </style>