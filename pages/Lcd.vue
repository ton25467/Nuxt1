<template>
  <div class="wrapper">
    <div class="hud-circle">
      <!-- Dotted ring via SVG -->
      <svg class="ring" viewBox="0 0 220 220" width="220" height="220">
        <circle
          v-for="i in 60"
          :key="i"
          :cx="110 + 103 * Math.cos(((i * 6) - 90) * Math.PI / 180)"
          :cy="110 + 103 * Math.sin(((i * 6) - 90) * Math.PI / 180)"
          r="3"
          fill="#00ff41"
          :opacity="i % 5 === 0 ? 1 : 0.45"
        />
      </svg>

      <!-- Inner content -->
      <div class="inner">
        <span class="date">{{ currentDate }}</span>
        <span class="time">{{ currentTime }}</span>
        <span class="temp">{{ temp }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const temp = ref('37.5')
const currentDate = ref('D/M/Y')
const currentTime = ref('TIME')

let timer = null

function tick() {
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

onMounted(() => { tick(); timer = setInterval(tick, 1000) })
onUnmounted(() => clearInterval(timer))
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@700;900&display=swap');

.wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100vh;
  background: #fff;
}

.hud-circle {
  position: relative;
  width: 220px;
  height: 220px;
  border-radius: 50%;
  background: radial-gradient(circle at center, #111 60%, #000 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow:
    0 0 0 3px #111,
    0 0 30px rgba(0, 255, 65, 0.15),
    inset 0 0 40px rgba(0, 0, 0, 0.8);
}

.ring {
  position: absolute;
  top: 0; left: 0;
  animation: spin 20s linear infinite;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

.inner {
  position: relative;
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2px;
  text-align: center;
}

.date {
  font-family: 'Orbitron', monospace;
  font-size: 0.7rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 8px #00ff41, 0 0 16px #00ff41;
  letter-spacing: 0.15em;
}

.time {
  font-family: 'Orbitron', monospace;
  font-size: 0.65rem;
  font-weight: 700;
  color: #00ff41;
  text-shadow: 0 0 8px #00ff41, 0 0 16px #00ff41;
  letter-spacing: 0.1em;
}

.temp {
  font-family: 'Orbitron', monospace;
  font-size: 2.6rem;
  font-weight: 900;
  color: #00ff41;
  text-shadow:
    0 0 10px #00ff41,
    0 0 25px #00ff41,
    0 0 50px #00ff41;
  line-height: 1.1;
  margin-top: 4px;
}
</style>