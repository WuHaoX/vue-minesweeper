<script setup lang="ts">
import { GamePlay } from '~/composables/logic'
import { isDev } from '~/composables/storage'

function toggleDev() {
  isDev.value = !isDev.value
}

const play = new GamePlay(12, 12, 30)
// vue-use函数
useStorage('vueSweeper-state', play.state)
const state = computed(() => play.board)
const mineCoun = computed(() => {
  return play.blocks.reduce((a, b) => a + (b.mine ? 1 : 0), 0)
})
watchEffect(() => {
  play.checkGameState()
})
</script>

<template>
  <div>
    Minesweeper
    <button @click="toggleDev()">
      {{ isDev }}
    </button>
    <div p-5>
      <div
        v-for="row, y in state"
        :key="y"
        flex="~"
        items-center justify-center
      >
        <MineBlock
          v-for="block, x in row"
          :key="x"
          :block="block"
          @click="play.onClick(block)"
          @contextmenu.prevent="play.onRightClick(block)"
        />
      </div>
    </div>

    <div>
      count: {{ mineCoun }}
    </div>

    <div flex="~ gap-1" justify-center>
      <button btn @click="toggleDev()">
        {{ isDev ? 'DEV' : 'NORMAL' }}
      </button>
      <button btn @click="play.reset()">
        RESET
      </button>
    </div>
  </div>
</template>
