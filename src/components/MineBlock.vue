<script setup lang="ts">
import { Icon } from '@iconify/vue'
import type { BlockState } from '~/types'
import { isDev } from '~/composables/storage'

defineProps<{ block: BlockState }>()

const numberColors = [
  'text-transparent',
  'text-blue-500',
  'text-green-500',
  'text-yellow-500',
  'text-orange-500',
  'text-red-500',
  'text-purple-500',
  'text-pink-500',
  'text-teal-500',
]

function getBlockClass(block: BlockState) {
  if (block.flagged)
    return 'bg-gray-500/10'

  if (!block.revealed)
    return 'bg-gray-500/10 hover:bg-gray-500/20'
  return block.mine ? 'bg-red-500/50' : numberColors[block.adjaccentttMines]
}
</script>

<template>
  <button
    flex="~"
    items-center justify-center
    w-10 h-10 m="0.25"
    :class="getBlockClass(block)"
    border="1 gray-400/10"
  >
    <template v-if="block.flagged">
      <div><Icon icon="mdi:flag" text-red /></div>
    </template>
    <template v-else-if="block.revealed || isDev">
      <div v-if="block.mine">
        <Icon icon="mdi:mine" />
      </div>
      <div v-else font-bold>
        {{ block.adjaccentttMines }}
      </div>
    </template>
  </button>
</template>
