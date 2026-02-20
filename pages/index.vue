<template>
  <div class="hud-container">
    <!-- Scanline overlay -->
    <div class="scanlines"></div>

    <!-- Left Panel: Main Temperature Display -->
    <div class="left-panel">
      <!-- Corner decorations -->
      <div class="corner-decoration top-left">
        <svg width="120" height="60" viewBox="0 0 120 60">
          <polyline points="0,60 0,0 120,0" stroke="#00ff41" stroke-width="2" fill="none"/>
          <circle cx="120" cy="0" r="4" fill="#00ff41" class="pulse-dot"/>
          <line x1="40" y1="0" x2="40" y2="12" stroke="#00ff41" stroke-width="1.5"/>
          <line x1="80" y1="0" x2="80" y2="8" stroke="#00ff41" stroke-width="1.5"/>
        </svg>
      </div>
      <div class="corner-decoration bottom-left">
        <svg width="120" height="60" viewBox="0 0 120 60">
          <polyline points="0,0 0,60 120,60" stroke="#00ff41" stroke-width="2" fill="none"/>
        </svg>
      </div>

      <!-- Dotted circle display -->
      <div class="circle-display">
        <svg class="dotted-ring" viewBox="0 0 400 400" width="400" height="400">
          <circle
            v-for="i in 72"
            :key="i"
            :cx="200 + 185 * Math.cos((i * 5 - 90) * Math.PI / 180)"
            :cy="200 + 185 * Math.sin((i * 5 - 90) * Math.PI / 180)"
            r="5"
            fill="#00ff41"
            :opacity="0.4 + 0.6 * ((i % 6 === 0) ? 1 : 0.5)"
            :class="{ 'dot-glow': i % 6 === 0 }"
          />
        </svg>

        <div class="circle-content">
          <div class="datetime-display">
            <span class="date-text">{{ currentDate }}</span>
            <span class="time-text">{{ currentTime }}</span>
          </div>
          <div class="main-temp">{{ bodyTemp }}</div>
          <div class="temp-unit">°C</div>
        </div>
      </div>
    </div>

    <!-- Center Panel: Room Stats -->
    <div class="center-panel">
      <!-- Room Temperature -->
      <div class="stat-circle">
        <div class="stat-circle-ring"></div>
        <div class="stat-circle-content">
          <div class="stat-label">ROOM<br>TEMPRATURE</div>
          <div class="stat-value">{{ roomTemp }}</div>
        </div>
      </div>

      <!-- Room Humidity -->
      <div class="stat-circle">
        <div class="stat-circle-ring"></div>
        <div class="stat-circle-content">
          <div class="stat-label">ROOM<br>Humidity</div>
          <div class="stat-value">{{ humidity }}%</div>
        </div>
      </div>

      <!-- Bottom circuit decoration -->
      <div class="circuit-decoration">
        <svg width="260" height="60" viewBox="0 0 260 60">
          <line x1="0" y1="30" x2="80" y2="30" stroke="#00ff41" stroke-width="1.5"/>
          <line x1="80" y1="30" x2="100" y2="10" stroke="#00ff41" stroke-width="1.5"/>
          <line x1="100" y1="10" x2="180" y2="10" stroke="#00ff41" stroke-width="1.5"/>
          <rect x="120" y="4" width="20" height="12" fill="#00ff41" opacity="0.5"/>
          <rect x="148" y="4" width="14" height="12" fill="#00ff41" opacity="0.3"/>
          <line x1="180" y1="10" x2="200" y2="30" stroke="#00ff41" stroke-width="1.5"/>
          <line x1="200" y1="30" x2="260" y2="30" stroke="#00ff41" stroke-width="1.5"/>
          <circle cx="260" cy="30" r="5" fill="#00ff41" class="pulse-dot"/>
        </svg>
      </div>
    </div>

    <!-- Right Panel: Log -->
    <div class="right-panel">
      <div class="log-frame">
        <!-- Frame corners -->
        <div class="log-corner tl"></div>
        <div class="log-corner tr"></div>
        <div class="log-corner bl"></div>
        <div class="log-corner br"></div>

        <div class="log-title">LOG</div>
        <div class="log-entries">
          <div
            v-for="(entry, i) in logEntries"
            :key="i"
            class="log-entry"
            :style="{ animationDelay: `${i * 0.1}s` }"
          >
            {{ entry.date }} / {{ entry.month }} / {{ entry.year }} | {{ entry.time }} : {{ entry.temp }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const currentDate = ref('D/M/Y')
const currentTime = ref('TIME')
const bodyTemp = ref('37.5')
const roomTemp = ref(25)
const humidity = ref(30)

const logEntries = ref([
  { date: '20', month: '02', year: '25', time: '08:00', temp: '36.8°C' },
  { date: '20', month: '02', year: '25', time: '10:00', temp: '37.1°C' },
  { date: '20', month: '02', year: '25', time: '12:00', temp: '37.5°C' },
  { date: '20', month: '02', year: '25', time: '14:00', temp: '37.8°C' },
  { date: '20', month: '02', year: '25', time: '16:00', temp: '37.2°C' },
  { date: '20', month: '02', year: '25', time: '18:00', temp: '36.9°C' },
  { date: '20', month: '02', year: '25', time: '20:00', temp: '36.7°C' },
])

let timer = null

function updateTime() {
  const now = new Date()
  const d = String(now.getDate()).padStart(2, '0')
  const m = String(now.getMonth() + 1).padStart(2, '0')
  const y = String(now.getFullYear()).slice(-2)
  const h = String(now.getHours()).padStart(2, '0')
  const min = String(now.getMinutes()).padStart(2, '0')
  const s = String(now.getSeconds()).padStart(2, '0')
  currentDate.value = `${d}/${m}/${y}`
  currentTime.value = `${h}:${min}:${s}`
}

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 1000)
})

onUnmounted(() => clearInterval(timer))
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.hud-container {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: #000;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 30px 40px;
  font-family: 'Orbitron', monospace;
  overflow: hidden;
  gap: 20px;
}

/* Scanlines */
.scanlines {
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 255, 65, 0.03) 2px,
    rgba(0, 255, 65, 0.03) 4px
  );
  pointer-events: none;
  z-index: 10;
}

/* ── LEFT PANEL ── */
.left-panel {
  position: relative;
  flex: 0 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
}

.corner-decoration {
  position: absolute;
}
.corner-decoration.top-left { top: -20px; left: -20px; }
.corner-decoration.bottom-left { bottom: -20px; left: -20px; }

.circle-display {
  position: relative;
  width: 400px;
  height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.dotted-ring {
  position: absolute;
  top: 0; left: 0;
  animation: spin 30s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.dot-glow {
  filter: drop-shadow(0 0 6px #00ff41);
}

.circle-content {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  z-index: 2;
}

.datetime-display {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.date-text {
  font-family: 'Orbitron', monospace;
  font-size: 1.1rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 10px #00ff41, 0 0 20px #00ff41;
  letter-spacing: 0.15em;
}

.time-text {
  font-family: 'Orbitron', monospace;
  font-size: 1.2rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 10px #00ff41, 0 0 20px #00ff41;
  letter-spacing: 0.1em;
}

.main-temp {
  font-family: 'Orbitron', monospace;
  font-size: 5.5rem;
  font-weight: 900;
  color: #00ff41;
  text-shadow: 0 0 20px #00ff41, 0 0 40px #00ff41, 0 0 60px #00ff41;
  line-height: 1;
  letter-spacing: -0.02em;
}

.temp-unit {
  font-family: 'Orbitron', monospace;
  font-size: 1.5rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 10px #00ff41;
  align-self: flex-end;
  margin-top: -10px;
}

/* ── CENTER PANEL ── */
.center-panel {
  flex: 0 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
}

.stat-circle {
  position: relative;
  width: 180px;
  height: 180px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.stat-circle-ring {
  position: absolute;
  inset: 0;
  border-radius: 50%;
  border: 2px dashed rgba(0, 255, 65, 0.5);
  box-shadow: 0 0 15px rgba(0, 255, 65, 0.2), inset 0 0 15px rgba(0, 255, 65, 0.05);
}

.stat-circle-content {
  text-align: center;
  z-index: 1;
}

.stat-label {
  font-family: 'Orbitron', monospace;
  font-size: 0.6rem;
  font-weight: 700;
  color: rgba(0, 255, 65, 0.8);
  text-transform: uppercase;
  letter-spacing: 0.1em;
  line-height: 1.4;
  margin-bottom: 8px;
}

.stat-value {
  font-family: 'Orbitron', monospace;
  font-size: 2.5rem;
  font-weight: 900;
  color: #00ff41;
  text-shadow: 0 0 15px #00ff41, 0 0 30px #00ff41;
}

.circuit-decoration {
  margin-top: 10px;
}

/* ── RIGHT PANEL ── */
.right-panel {
  flex: 0 0 380px;
  height: calc(100vh - 60px);
  display: flex;
  align-items: stretch;
}

.log-frame {
  position: relative;
  width: 100%;
  background: rgba(0, 255, 65, 0.03);
  border: 2px solid rgba(0, 255, 65, 0.4);
  padding: 24px 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* Frame corner accents */
.log-corner {
  position: absolute;
  width: 24px;
  height: 24px;
}
.log-corner.tl { top: -2px; left: -2px; border-top: 4px solid #00ff41; border-left: 4px solid #00ff41; }
.log-corner.tr { top: -2px; right: -2px; border-top: 4px solid #00ff41; border-right: 4px solid #00ff41; }
.log-corner.bl { bottom: -2px; left: -2px; border-bottom: 4px solid #00ff41; border-left: 4px solid #00ff41; }
.log-corner.br { bottom: -2px; right: -2px; border-bottom: 4px solid #00ff41; border-right: 4px solid #00ff41; }

/* Side bar accents (like image) */
.log-frame::before {
  content: '';
  position: absolute;
  top: 10%;
  left: -8px;
  width: 6px;
  height: 80%;
  background: #00ff41;
  box-shadow: 0 0 10px #00ff41;
}
.log-frame::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 6px;
  background: #00ff41;
  box-shadow: 0 0 12px #00ff41;
}

.log-title {
  font-family: 'Orbitron', monospace;
  font-size: 1.6rem;
  font-weight: 900;
  color: #00ff41;
  text-shadow: 0 0 10px #00ff41, 0 0 20px #00ff41;
  letter-spacing: 0.2em;
  margin-bottom: 8px;
}

.log-entries {
  display: flex;
  flex-direction: column;
  gap: 14px;
  flex: 1;
  justify-content: space-around;
}

.log-entry {
  font-family: 'Orbitron', monospace;
  font-size: 0.85rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 8px #00ff41;
  letter-spacing: 0.08em;
  padding: 6px 0;
  border-bottom: 1px solid rgba(0, 255, 65, 0.15);
  animation: fadeSlideIn 0.5s ease both;
}

@keyframes fadeSlideIn {
  from { opacity: 0; transform: translateX(20px); }
  to { opacity: 1; transform: translateX(0); }
}

/* Pulse animation for dots */
.pulse-dot {
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; r: 4; }
  50% { opacity: 0.3; r: 6; }
}

/* Global glow flicker */
@keyframes flicker {
  0%, 100% { opacity: 1; }
  92% { opacity: 1; }
  93% { opacity: 0.8; }
  94% { opacity: 1; }
  96% { opacity: 0.9; }
  97% { opacity: 1; }
}

.hud-container {
  animation: flicker 8s infinite;
}
</style>