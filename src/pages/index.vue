<script setup lang="ts">
import { Icon } from '@iconify/vue';
import {BlockState} from '~/types'

const WIDTH = 10
const HEIGHT = 10
const state = ref(
  Array.from({ length: HEIGHT },(_, y) => 
    Array.from({ length: WIDTH },
      (_, x): BlockState => ({
        x,y,adjaccentttMines: 0,revealed:false,
      }),
    ),
  ),
)

function generateMines(initial: BlockState){
  for(const row of state.value){
    for( const block of row){
      if(Math.abs(initial.x - block.x) <= 1)
        continue
      if(Math.abs(initial.y - block.y) <= 1)
        continue
      block.mine = Math.random() < 0.2
    }
  }
  updateNumbers()
}
const directions = [
  [1,1],
  [1,0],
  [1,-1],
  [0,-1],
  [-1,-1],
  [-1,0],
  [-1,1],
  [0,1],
]

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

function updateNumbers() {
  state.value.forEach((raw,y)=>{
    raw.forEach((block,x)=>{
      if(block.mine)
        return
        getSiblings(block).forEach(b=> {
          if(b.mine)
            block.adjaccentttMines++
        })
    })
  })
}

function expendZero(block: BlockState){
  if(block.adjaccentttMines)
  return
  getSiblings(block).forEach(s=> {
    if(!s.revealed){
      s.revealed =true
      expendZero(s)
    }
  })
}

let mineGenerated = false
let dev = false

function onClick(e: MouseEvent, block: BlockState){
  console.log(e)
  if(!mineGenerated){
    generateMines(block)
    mineGenerated = true
  }

  block.revealed = true
  if(block.mine){
    alert("您输了!!!")
  }
  expendZero(block)
  checkGameState()
}

function onRightClick(block: BlockState){
  if(block.revealed)
    return 
  block.flagged = !block.flagged
  checkGameState()
}

function getBlockClass(block : BlockState){
  if(block.flagged){
    return 'bg-gray-500/10'
  }
  if(!block.revealed)
    return 'bg-gray-500/10 hover:bg-gray-500/20'
  return block.mine ? 'bg-red-500/50' : numberColors[block.adjaccentttMines]
}

function getSiblings(block: BlockState) {
  return  directions.map(([dx, dy]) => {
    const x2 = block.x + dx;
    const y2 = block.y + dy;
    if (x2 < 0 || x2 >= WIDTH || y2 < 0 || y2 >= HEIGHT) {
      return undefined;
    }
    return state.value[y2][x2]
  })
  .filter(Boolean) as BlockState[];
}
function checkGameState(){
  const blocks =state.value.flat()
  if(blocks.every(block => block.revealed || block.flagged)){
    if(blocks.some(block => block.flagged && !block.mine))
      alert('you cheat!')
    else
      alert('you win!')
  }
}
</script>

<template>
  <div>
    Minesweeper
    <div p-5>
      <div v-for="row, y in state"
          :key="y"
          flex="~"
          items-center justify-center>
        <button v-for="block ,x in row"
                :key="x"
                flex="~"
                items-center justify-center
                w-10 h-10 m="0.25"
                @click="onClick($event, block)"
                @contextmenu.prevent="onRightClick(block)"
                :class="getBlockClass(block)"
                border="1 gray-400/10">
          <template v-if="block.flagged">
            <div><Icon icon="mdi:flag" text-red /></div>
          </template>
          <template v-else-if="block.revealed || dev">
            <div v-if="block.mine"><Icon icon="mdi:mine" /></div>
            <div v-else>
              {{ block.adjaccentttMines }}
            </div>
          </template>
        </button>
      </div>
    </div>
  </div>
</template>
