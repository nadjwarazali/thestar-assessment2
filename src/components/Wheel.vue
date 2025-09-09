<script setup>
import { ref } from 'vue'
import prizesData from '@/data/prizes.json'

const prizes = ref(prizesData)
const rotation = ref(0)
const isSpinning = ref(false)
const result = ref(null)

function getWheelBackground() {
  const slices = prizes.value.length
  const colors = ['#fbbf24', '#3b82f6']
  const step = 360 / slices
  let gradient = 'conic-gradient('
  prizes.value.forEach((_, i) => {
    const color = colors[i % colors.length]
    gradient += `${color} ${i * step}deg ${(i + 1) * step}deg, `
  })
  return gradient.slice(0, -2) + ')'
}

function getLabelStyle(i) {
  const slices = prizes.value.length
  const angle = (360 / slices) * i + 360 / (slices * 2)
  return {
    transform: `rotate(${angle}deg) translate(0, -130px) rotate(-${angle}deg)`,
  }
}

function spin() {
  if (isSpinning.value) return
  isSpinning.value = true
  result.value = null

  const spins = 5 * 360
  const stop = Math.floor(Math.random() * 360)
  const final = rotation.value + spins + stop
  rotation.value = final

  setTimeout(() => {
    isSpinning.value = false
    const sliceAngle = 360 / prizes.value.length
    const index = Math.floor(((360 - (final % 360)) % 360) / sliceAngle) % prizes.value.length
    result.value = prizes.value[index]
  }, 4000)
}
</script>

<template>
  <div class="flex flex-col items-center">
    <div class="relative w-72 h-72 mb-6">
      <div
        class="absolute inset-0 rounded-full transition-transform duration-[4000ms] ease-out"
        :style="{ transform: `rotate(${rotation}deg)`, background: getWheelBackground() }"
      >
        <div
          v-for="(prize, i) in prizes"
          :key="i"
          class="absolute top-1/2 left-1/2 text-white font-bold text-sm drop-shadow"
          :style="getLabelStyle(i)"
        >
          {{ prize }}
        </div>
      </div>

      <div class="absolute -top-6 left-1/2 -translate-x-1/2">
        <div
          class="w-0 h-0 border-l-[15px] border-r-[15px] border-b-[25px] border-l-transparent border-r-transparent border-b-red-600"
        ></div>
      </div>
    </div>

    <button
      @click="spin"
      :disabled="isSpinning"
      class="px-6 py-2 rounded-lg text-white font-semibold"
      :class="isSpinning ? 'bg-gray-400 cursor-not-allowed' : 'bg-blue-600 hover:bg-blue-700'"
    >
      {{ isSpinning ? 'Spinning...' : 'Spin the Wheel' }}
    </button>

    <p v-if="result" class="mt-4 text-lg font-semibold text-white">You won: {{ result }}!</p>
  </div>
</template>
