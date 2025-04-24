<template>
  <div class="environment-app">
    <a href="/" class="fixed bottom-5 right-5 btn btn-circle btn-success shadow-lg">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" 
          viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" 
            stroke-linejoin="round" stroke-width="2" 
            d="M9 5l7 7-7 7" />
        </svg>
    </a>
    <!-- Header dengan lokasi dan waktu -->
    <div class="app-header">
      <div class="location-time">
        <h1>🌤️ Lapor Cuaca & Udara</h1>
        <div class="location">
          <span class="pin">📍</span>
          <span class="city">{{ city }}</span>
        </div>
        <div class="current-time">{{ currentTime }}</div>
      </div>
      <div class="last-updated">
        Terakhir diperbarui: {{ lastUpdated }}
        <button @click="refreshData" class="refresh-button">🔄</button>
      </div>
    </div>

    <!-- Dashboard utama dengan grid responsif -->
    <div class="dashboard">
      <!-- Panel Cuaca -->
      <div class="weather-panel panel">
        <div class="panel-header">
          <h2>Kondisi Cuaca</h2>
          <div class="weather-icon">
            <img :src="getWeatherIcon(weather.temp, weather.description)" alt="Weather icon">
          </div>
        </div>
        
        <div class="weather-main">
          <div class="temperature">
            <span class="temp-value">{{ weather.temp }}°C</span>
            <span class="temp-feel">Terasa seperti {{ weather.feels_like }}°C</span>
          </div>
          <div class="weather-description">{{ capitalizeFirstLetter(weather.description) }}</div>
        </div>

        <div class="weather-details">
          <div class="detail-item">
            <span class="detail-icon">💧</span>
            <span class="detail-value">{{ weather.humidity }}%</span>
            <span class="detail-label">Kelembaban</span>
          </div>
          <div class="detail-item">
            <span class="detail-icon">🌬️</span>
            <span class="detail-value">{{ weather.wind_speed }} km/jam</span>
            <span class="detail-label">Angin</span>
          </div>
          <div class="detail-item">
            <span class="detail-icon">☔</span>
            <span class="detail-value">{{ weather.rain ? weather.rain + 'mm' : '0mm' }}</span>
            <span class="detail-label">Hujan</span>
          </div>
        </div>

        <div class="hourly-forecast" v-if="hourlyForecast.length > 0">
          <h3>Perkiraan per Jam</h3>
          <div class="hourly-scroll">
            <div class="hour-item" v-for="(hour, index) in hourlyForecast" :key="index">
              <div class="hour-time">{{ hour.time }}</div>
              <img :src="getWeatherIcon(hour.temp, hour.description)" class="hour-icon" alt="Hourly weather">
              <div class="hour-temp">{{ hour.temp }}°</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Panel Kualitas Udara -->
      <div class="air-panel panel" :class="airQualityClass">
        <div class="panel-header">
          <h2>Kualitas Udara</h2>
          <div class="aqi-display">
            <span class="aqi-value">{{ air.aqi }}</span>
            <span class="aqi-label">AQI</span>
          </div>
        </div>

        <div class="air-quality">
          <div class="quality-status">{{ airQuality.text }}</div>
          <div class="quality-icon" :class="getSmileyClass(air.aqi)"></div>
        </div>

        <div class="air-pollutants">
          <h3>Polutan Utama</h3>
          <div class="pollutant-item">
            <span class="pollutant-name">PM2.5</span>
            <div class="pollutant-bar-container">
              <div class="pollutant-bar" :style="{ width: getPollutantPercentage(air.components.pm2_5, 50) }"></div>
            </div>
            <span class="pollutant-value">{{ air.components.pm2_5 }} µg/m³</span>
          </div>
          <div class="pollutant-item">
            <span class="pollutant-name">PM10</span>
            <div class="pollutant-bar-container">
              <div class="pollutant-bar" :style="{ width: getPollutantPercentage(air.components.pm10, 100) }"></div>
            </div>
            <span class="pollutant-value">{{ air.components.pm10 }} µg/m³</span>
          </div>
          <div class="pollutant-item">
            <span class="pollutant-name">CO</span>
            <div class="pollutant-bar-container">
              <div class="pollutant-bar" :style="{ width: getPollutantPercentage(air.components.co, 5000) }"></div>
            </div>
            <span class="pollutant-value">{{ air.components.co }} µg/m³</span>
          </div>
        </div>

        <div class="air-info">
          <h3>Info Kesehatan</h3>
          <p>{{ airQuality.healthMessage }}</p>
          <div class="air-sources">
            <span>Sumber data: </span>
            <a href="https://www.who.int/health-topics/air-pollution" target="_blank">WHO</a>,
            <a href="https://www.epa.gov/air-trends" target="_blank">EPA</a>
          </div>
        </div>
      </div>
    </div>

    <!-- Tips dan Fakta -->
    <div class="tips-facts">
      <div class="health-tips" v-if="air.aqi >= 3">
        <div class="tips-header">
          <span class="warning-icon">⚠️</span>
          <h3>Tips Kesehatan Hari Ini</h3>
        </div>
        <ul class="tips-list">
          <li v-for="(tip, index) in healthTips" :key="index">{{ tip }}</li>
        </ul>
      </div>

      <div class="fun-section">
        <div class="fun-fact">
          <div class="fact-header">
            <span class="fact-icon">💡</span>
            <h3>Fakta Menarik</h3>
          </div>
          <p>{{ currentFunFact }}</p>
        </div>

        <div class="air-care">
          <div class="care-header">
            <span class="care-icon">🌱</span>
            <h3>Cara Menjaga Udara</h3>
          </div>
          <p>{{ currentAirCareTip }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      city: 'Mendeteksi lokasi...',
      currentTime: '',
      lastUpdated: '',
      weather: {
        temp: 0,
        feels_like: 0,
        humidity: 0,
        wind_speed: 0,
        description: '',
        rain: 0
      },
      air: {
        aqi: 1,
        components: {
          co: 0,
          pm2_5: 0,
          pm10: 0
        }
      },
      hourlyForecast: [],
      apiKey: 'cfd44121a55885bcd7f8d90f9fa77e2c',
      healthTips: [
        "Pakai masker saat keluar rumah",
        "Hindari olahraga di luar ruangan",
        "Tutup jendela untuk mengurangi polusi masuk",
        "Gunakan air purifier jika memungkinkan",
        "Tanam tanaman pembersih udara di rumah"
      ],
      funFacts: [
        "PM2.5 adalah partikel halus yang bisa masuk langsung ke paru-paru dan aliran darah!",
        "Kualitas udara biasanya paling baik di pagi hari sebelum jam sibuk!",
        "1 pohon dewasa bisa menyerap 4.5 kg polusi udara per tahun!",
        "Polusi udara berkurang drastis saat hujan turun!",
        "Penggunaan transportasi umum membantu mengurangi polusi udara hingga 30%!"
      ],
      airCareTips: [
        "Kurangi penggunaan kendaraan pribadi, gunakan transportasi umum",
        "Tanam lebih banyak tanaman di sekitar rumah Anda",
        "Hindari membakar sampah sembarangan",
        "Gunakan peralatan hemat energi di rumah",
        "Dukung penggunaan energi terbarukan"
      ],
      currentFunFact: '',
      currentAirCareTip: '',
      updateInterval: null
    }
  },
  computed: {
    airQuality() {
      const levels = [
        { 
          text: "SANGAT BAIK", 
          healthMessage: "Kualitas udara sangat baik, ideal untuk aktivitas luar ruangan.",
          color: "#2ecc71"
        },
        { 
          text: "BAIK", 
          healthMessage: "Kualitas udara baik, aman untuk kebanyakan orang.",
          color: "#27ae60"
        },
        { 
          text: "SEDANG", 
          healthMessage: "Kualitas udara sedang, mungkin berpengaruh pada orang sensitif.",
          color: "#f39c12"
        },
        { 
          text: "TIDAK SEHAT", 
          healthMessage: "Kualitas udara tidak sehat, kelompok sensitif harus mengurangi aktivitas luar.",
          color: "#e74c3c"
        },
        { 
          text: "SANGAT TIDAK SEHAT", 
          healthMessage: "Kualitas udara sangat tidak sehat, semua orang mungkin merasakan efek kesehatan.",
          color: "#9b59b6"
        },
        { 
          text: "BERBAHAYA", 
          healthMessage: "Kualitas udara berbahaya, hindari semua aktivitas di luar ruangan.",
          color: "#e84393"
        }
      ]
      return levels[this.air.aqi - 1] || levels[0]
    },
    airQualityClass() {
      return `aqi-level-${this.air.aqi}`
    }
  },
  created() {
    this.currentFunFact = this.getRandomFunFact()
    this.currentAirCareTip = this.getRandomAirCareTip()
  },
  mounted() {
    this.getLocationAndFetch()
    this.updateCurrentTime()
    this.updateInterval = setInterval(this.updateCurrentTime, 60000)
  },
  beforeDestroy() {
    clearInterval(this.updateInterval)
  },
  methods: {
    async getLocationAndFetch() {
      try {
        const position = await new Promise((resolve, reject) => {
          if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(resolve, reject)
          } else {
            resolve({
              coords: {
                latitude: -6.2088,
                longitude: 106.8456
              }
            })
          }
        })

        const lat = position.coords.latitude
        const lon = position.coords.longitude

        const geoResponse = await axios.get(`https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`)
        this.city = 
          geoResponse.data.address.city ||
          geoResponse.data.address.town ||
          geoResponse.data.address.village ||
          geoResponse.data.address.county ||
          geoResponse.data.address.state ||
          'Lokasi Anda'

        const weatherUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&lang=id&appid=${this.apiKey}`
        const airUrl = `https://api.openweathermap.org/data/2.5/air_pollution?lat=${lat}&lon=${lon}&appid=${this.apiKey}`
        const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&units=metric&lang=id&appid=${this.apiKey}`

        const [weatherRes, airRes, forecastRes] = await Promise.all([
          axios.get(weatherUrl),
          axios.get(airUrl),
          axios.get(forecastUrl)
        ])

        this.weather = {
          temp: Math.round(weatherRes.data.main.temp),
          feels_like: Math.round(weatherRes.data.main.feels_like),
          humidity: weatherRes.data.main.humidity,
          wind_speed: Math.round(weatherRes.data.wind.speed * 3.6),
          description: weatherRes.data.weather[0].description,
          rain: weatherRes.data.rain ? weatherRes.data.rain['1h'] : 0
        }

        const airData = airRes.data.list[0]
        this.air = {
          aqi: airData.main.aqi,
          components: {
            co: Math.round(airData.components.co * 100) / 100,
            pm2_5: Math.round(airData.components.pm2_5 * 10) / 10,
            pm10: Math.round(airData.components.pm10 * 10) / 10
          }
        }

        this.processHourlyForecast(forecastRes.data)
        this.lastUpdated = this.formatTime(new Date())

      } catch (error) {
        console.error('Error fetching data:', error)
        this.city = 'Lokasi Anda'
        this.weather = {
          temp: 28,
          feels_like: 30,
          humidity: 65,
          wind_speed: 12,
          description: 'cerah berawan',
          rain: 0
        }
        this.air = {
          aqi: 2,
          components: {
            co: 0.21,
            pm2_5: 12.5,
            pm10: 23.7
          }
        }
        this.lastUpdated = this.formatTime(new Date())
        this.generateSampleHourlyData()
      }
    },
    processHourlyForecast(forecastData) {
      this.hourlyForecast = forecastData.list.slice(0, 8).map(item => {
        const date = new Date(item.dt * 1000)
        return {
          time: this.formatHour(date),
          temp: Math.round(item.main.temp),
          description: item.weather[0].description
        }
      })
    },
    generateSampleHourlyData() {
      const hours = []
      const now = new Date()
      const currentHour = now.getHours()
      
      for (let i = 0; i < 8; i++) {
        const hour = (currentHour + i) % 24
        hours.push({
          time: `${hour}:00`,
          temp: 28 + Math.floor(Math.random() * 5) - 2,
          description: ['cerah', 'berawan', 'cerah berawan', 'hujan ringan'][Math.floor(Math.random() * 4)]
        })
      }
      
      this.hourlyForecast = hours
    },
    updateCurrentTime() {
      const now = new Date()
      this.currentTime = this.formatTime(now)
    },
    formatTime(date) {
      const options = { 
        weekday: 'long',
        day: 'numeric',
        month: 'long',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      }
      return date.toLocaleDateString('id-ID', options)
    },
    formatHour(date) {
      return date.getHours() + ':00'
    },
    getWeatherIcon(temp, description) {
      const baseUrl = 'https://cdn-icons-png.flaticon.com/512/'
      
      if (description.includes('hujan')) {
        return baseUrl + '1146/1146860.png'
      } else if (description.includes('mendung') || description.includes('berawan')) {
        return baseUrl + '1146/1146865.png'
      } else if (temp > 30) {
        return baseUrl + '3222/3222807.png'
      } else if (temp > 20) {
        return baseUrl + '3222/3222675.png'
      } else {
        return baseUrl + '3222/3222691.png'
      }
    },
    getSmileyClass(aqi) {
      const classes = ['happy', 'ok', 'neutral', 'sick', 'danger', 'critical']
      return classes[aqi - 1] || 'neutral'
    },
    getPollutantPercentage(value, max) {
      const percentage = (value / max) * 100
      return Math.min(percentage, 100) + '%'
    },
    getRandomFunFact() {
      return this.funFacts[Math.floor(Math.random() * this.funFacts.length)]
    },
    getRandomAirCareTip() {
      return this.airCareTips[Math.floor(Math.random() * this.airCareTips.length)]
    },
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1)
    },
    refreshData() {
      this.getLocationAndFetch()
    },
  }
}
</script>

<style scoped>
/* Base Styles */
.environment-app {
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(to bottom, #f5f7fa, #e4e8eb);
  min-height: 100vh;
}

.app-header {
  background: linear-gradient(135deg, #4b6cb7, #182848);
  color: white;
  padding: 20px;
  border-radius: 15px;
  margin-bottom: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.location-time h1 {
  margin: 0;
  font-size: clamp(1.5rem, 4vw, 1.8rem);
  display: flex;
  align-items: center;
  gap: 10px;
}

.location {
  display: flex;
  align-items: center;
  margin: 10px 0;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.pin {
  margin-right: 8px;
}

.current-time, .last-updated {
  font-size: clamp(0.8rem, 2.5vw, 0.9rem);
  opacity: 0.9;
}

.last-updated {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  margin-top: 10px;
  gap: 10px;
}

.refresh-button {
  background: none;
  border: none;
  color: white;
  font-size: 1.2rem;
  cursor: pointer;
  transition: transform 0.3s;
}

.refresh-button:hover {
  transform: rotate(180deg);
}

/* Dashboard Responsive */
.dashboard {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  margin-bottom: 20px;
}

@media (min-width: 768px) {
  .dashboard {
    grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  }
}

.panel {
  background: white;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  min-width: 0; /* Prevent grid blowout */
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  border-bottom: 1px solid #eee;
  padding-bottom: 15px;
}

.panel-header h2 {
  margin: 0;
  color: #2c3e50;
  font-size: clamp(1.2rem, 3vw, 1.4rem);
}

.weather-icon img {
  width: clamp(50px, 8vw, 60px);
  height: clamp(50px, 8vw, 60px);
}

.weather-main {
  text-align: center;
  margin: 20px 0;
}

.temperature {
  margin-bottom: 10px;
}

.temp-value {
  font-size: clamp(2.5rem, 8vw, 3.5rem);
  font-weight: bold;
  color: #e74c3c;
  line-height: 1;
}

.temp-feel {
  display: block;
  font-size: clamp(0.9rem, 2.5vw, 1rem);
  color: #7f8c8d;
  margin-top: 5px;
}

.weather-description {
  font-size: clamp(1rem, 3vw, 1.2rem);
  color: #34495e;
  margin-top: 10px;
}

.weather-details {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.detail-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.detail-icon {
  font-size: clamp(1.5rem, 4vw, 1.8rem);
  margin-bottom: 5px;
}

.detail-value {
  font-weight: bold;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.1rem);
}

.detail-label {
  font-size: clamp(0.75rem, 2vw, 0.85rem);
  color: #7f8c8d;
}

.hourly-forecast {
  margin-top: 25px;
}

.hourly-forecast h3 {
  margin-bottom: 15px;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.hourly-scroll {
  display: flex;
  overflow-x: auto;
  gap: 15px;
  padding-bottom: 10px;
  -webkit-overflow-scrolling: touch;
}

.hour-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 60px;
  flex-shrink: 0;
}

.hour-time {
  font-size: clamp(0.75rem, 2vw, 0.85rem);
  color: #7f8c8d;
  margin-bottom: 5px;
}

.hour-icon {
  width: clamp(35px, 6vw, 40px);
  height: clamp(35px, 6vw, 40px);
  margin-bottom: 5px;
}

.hour-temp {
  font-weight: bold;
  color: #2c3e50;
  font-size: clamp(0.9rem, 2.5vw, 1rem);
}

/* Air Quality Panel */
.aqi-display {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.aqi-value {
  font-size: clamp(1.5rem, 5vw, 2rem);
  font-weight: bold;
}

.aqi-label {
  font-size: clamp(0.7rem, 2vw, 0.8rem);
  opacity: 0.8;
}

.air-quality {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
}

.quality-status {
  font-size: clamp(1.2rem, 4vw, 1.5rem);
  font-weight: bold;
}

.quality-icon {
  width: clamp(50px, 8vw, 60px);
  height: clamp(50px, 8vw, 60px);
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
}

/* Styles for different smiley faces */
.quality-icon.happy {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583344.png');
}
.quality-icon.ok {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583437.png');
}
.quality-icon.neutral {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583450.png');
}
.quality-icon.sick {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583473.png');
}
.quality-icon.danger {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583466.png');
}
.quality-icon.critical {
  background-image: url('https://cdn-icons-png.flaticon.com/512/2583/2583475.png');
}

.air-pollutants {
  margin-top: 25px;
}

.air-pollutants h3 {
  margin-bottom: 15px;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.pollutant-item {
  display: grid;
  grid-template-columns: 70px 1fr 80px;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}

@media (max-width: 480px) {
  .pollutant-item {
    grid-template-columns: 60px 1fr 70px;
    gap: 8px;
  }
}

.pollutant-name {
  font-weight: bold;
  color: #2c3e50;
  font-size: clamp(0.85rem, 2.5vw, 1rem);
}

.pollutant-bar-container {
  height: 8px;
  background: #ecf0f1;
  border-radius: 4px;
  overflow: hidden;
}

.pollutant-bar {
  height: 100%;
  background: #e74c3c;
  border-radius: 4px;
}

.pollutant-value {
  font-size: clamp(0.75rem, 2vw, 0.85rem);
  color: #7f8c8d;
  text-align: right;
}

.air-info {
  margin-top: 25px;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.air-info h3 {
  margin-bottom: 10px;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.air-info p {
  margin-bottom: 10px;
  line-height: 1.5;
  font-size: clamp(0.85rem, 2.5vw, 1rem);
}

.air-sources {
  font-size: clamp(0.7rem, 2vw, 0.8rem);
  color: #7f8c8d;
  margin-top: 15px;
}

.air-sources a {
  color: #3498db;
  text-decoration: none;
}

.air-sources a:hover {
  text-decoration: underline;
}

/* Tips & Facts Section */
.tips-facts {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
}

.health-tips {
  background: white;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.tips-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 15px;
}

.tips-header h3 {
  margin: 0;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.warning-icon {
  font-size: clamp(1.2rem, 3.5vw, 1.5rem);
  color: #e74c3c;
}

.tips-list {
  padding-left: 20px;
  margin: 0;
}

.tips-list li {
  margin-bottom: 8px;
  line-height: 1.5;
  font-size: clamp(0.85rem, 2.5vw, 1rem);
}

.fun-section {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
}

@media (min-width: 600px) {
  .fun-section {
    grid-template-columns: repeat(2, 1fr);
  }
}

.fun-fact, .air-care {
  background: white;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

.fact-header, .care-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 15px;
}

.fact-header h3, .care-header h3 {
  margin: 0;
  color: #2c3e50;
  font-size: clamp(1rem, 3vw, 1.2rem);
}

.fact-icon {
  font-size: clamp(1.2rem, 3.5vw, 1.5rem);
  color: #f39c12;
}

.care-icon {
  font-size: clamp(1.2rem, 3.5vw, 1.5rem);
  color: #27ae60;
}

.fun-fact p, .air-care p {
  line-height: 1.5;
  margin: 0 0 15px 0;
  font-size: clamp(0.85rem, 2.5vw, 1rem);
}

.fact-button {
  background: #f39c12;
  color: white;
  border: none;
  padding: 8px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-size: clamp(0.8rem, 2.5vw, 0.9rem);
  transition: background 0.3s;
}

.fact-button:hover {
  background: #e67e22;
}

/* AQI Level Colors */
.aqi-level-1 {
  border-left: 6px solid #2ecc71;
}
.aqi-level-2 {
  border-left: 6px solid #27ae60;
}
.aqi-level-3 {
  border-left: 6px solid #f39c12;
}
.aqi-level-4 {
  border-left: 6px solid #e74c3c;
}
.aqi-level-5 {
  border-left: 6px solid #9b59b6;
}
.aqi-level-6 {
  border-left: 6px solid #e84393;
}

.aqi-level-1 .aqi-value {
  color: #2ecc71;
}
.aqi-level-2 .aqi-value {
  color: #27ae60;
}
.aqi-level-3 .aqi-value {
  color: #f39c12;
}
.aqi-level-4 .aqi-value {
  color: #e74c3c;
}
.aqi-level-5 .aqi-value {
  color: #9b59b6;
}
.aqi-level-6 .aqi-value {
  color: #e84393;
}

/* Animation */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.panel {
  animation: fadeIn 0.5s ease-out;
}

/* Responsive adjustments for very small screens */
@media (max-width: 400px) {
  .weather-details {
    grid-template-columns: 1fr;
  }
  
  .panel {
    padding: 15px;
  }
  
  .app-header {
    padding: 15px;
  }
}
</style>