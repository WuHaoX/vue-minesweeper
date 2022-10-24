<script setup lang="ts">
import confetti from 'canvas-confetti'

const props = defineProps<{
  passed: boolean
}>()

function randomInRange(min: number, max: number) {
  return Math.random() * (max - min) + min
}

function congrats() {
  const defaults = {
    colors: [
      '#1f1017',
      '#98251e',
      '#fffc11',
      '#8c5052',
      '#811e21',
    ],
    startVelocity: 30,
    spread: 360,
    ticks: 60,
    zIndex: 0,
  } as confetti.Options

  const duration = 15 * 1000
  const animationEnd = Date.now() + duration

  setInterval(() => {
    const timeLeft = animationEnd - Date.now()
    const particleCount = 50 * (timeLeft / duration)
    // since particles fall down, start a bit higher than random
    confetti(Object.assign({}, defaults, { particleCount, origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 } }))
    confetti(Object.assign({}, defaults, { particleCount, origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 } }))
  }, 250)
}
watch(() => props.passed, (v) => {
  if (v)
    setTimeout(congrats, 300)
}, { flush: 'post' })
</script>

<template>
  <div />
</template>
