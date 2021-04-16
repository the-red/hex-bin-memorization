<template>
  <div class="container mx-auto bg-gray-100">
    <template v-if="isStarted && index < hexes.length">
      <main class="p-4 sm:p-6 lg:p-8 ">
        <div class="text-center mb-6">
          <div class="text-hex" v-text="currentHex" />
        </div>
        <div class="mb-6">
          <template v-if="isAnswerShown">
            <div class="h-10 text-2xl text-center" v-text="currentAnswer" />
          </template>
          <template v-else>
            <div class="h-10" />
          </template>
        </div>
        <div class="flex lex-1 items-center justify-center mb-6">
          <button
            type="button"
            class="mr-12 inline-flex items-center px-4 py-2 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            @click="handleShowAnswer"
          >
            答え
          </button>
          <button
            type="button"
            class="inline-flex items-center px-4 py-2 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            @click="handleProceedToNext"
          >
            つぎへ
          </button>
        </div>
        <div class="text-center mb-10">
          <span class="text-2xl" v-text="index+1" />
          /
          <span class="text-2xl" v-text="hexes.length" />
          <span v-text="`（のこり${hexes.length - index - 1}）`" />
        </div>
        <div class="flex flex-1 items-center justify-center mb-6">
          <button
            type="button"
            class="restart-button inline-flex items-center px-4 py-2 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
            @click="handleStartWithConfirm"
          >
            さいしょからやりなおす
          </button>
        </div>
      </main>
    </template>
    <template v-else-if="isStarted && index >= hexes.length">
      <div class="flex flex-1 items-center justify-center height100">
        <div>
          <button type="button" class="mb-10 inline-flex items-center px-4 py-2 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="handleStart">
            おつかれさま！もう1回やる？
          </button>
          <div class="text-center">
            <span class="text" v-text="elapsedTimeString" />
          </div>
        </div>
      </div>
    </template>
    <template v-else>
      <div class="flex flex-col items-center justify-center height100">
        <div class="h-10 text-4xl text-center mb-24">
          16進数→2進数
        </div>
        <button type="button" class="inline-flex items-center mb-10 px-4 py-2 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500" @click="handleStart">
          はじめる！
        </button>
      </div>
    </template>
  </div>
</template>

<script>
import { shuffle } from 'lodash-es'

export default {
  components: {
  },
  props: {
  },
  data () {
    return {
      hexes: [],
      answers: [],
      index: 0,
      isStarted: false,
      isAnswerShown: false,
      elapsedTime: 0
    }
  },
  computed: {
    currentHex () {
      return this.hexes[this.index]
    },
    currentAnswer () {
      return this.answers[this.index]
    },
    elapsedTimeString () {
      const micro = this.zeroPadding((parseInt(this.elapsedTime / 10) % 100), 2)
      const sec = this.elapsedTime / 1000.0
      const minute = parseInt(sec / 60)
      const second = parseInt(sec) % 60

      return `${minute}分 ${second}秒 ${micro}`
    }
  },
  watch: {
    index () {
      if (this.isStarted && this.index >= this.hexes.length) {
        console.log('clear')
        clearInterval(this.timer)
      }
    }
  },
  created () {
  },
  methods: {
    zeroPadding (num, length) {
      return ('0000000000' + num).slice(-length)
    },
    handleStartWithConfirm () {
      const result = window.confirm('本当にやりなおす？')
      if (result) {
        this.handleStart()
      }
    },
    handleStart () {
      // TODO: 00～ffまでの2桁16進数も上級編として選べるように
      const base = [0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8, 0x9, 0xA, 0xB, 0xC, 0xD, 0xE, 0xF]
      const array = []
      base.forEach((num1) => {
        array.push(num1)
        // base.forEach((num2) => {
        //   array.push([num1, num2])
        // })
      })

      const hexes = []
      const answers = []

      shuffle(array).forEach((vals) => {
        const num1 = vals
        // const num2 = vals[1]
        answers.push(`${num1.toString(2).padStart(4, '0')}`)
        hexes.push((num1).toString(16))
      })

      this.hexes = hexes
      this.answers = answers
      this.isStarted = true
      this.index = 0
      this.elapsedTime = 0

      this.timer = setInterval(() => {
        this.elapsedTime += 10
      }, 10)
    },
    handleShowAnswer () {
      this.isAnswerShown = !this.isAnswerShown
    },
    handleProceedToNext () {
      this.index += 1
      this.isAnswerShown = false
    }
  }
}
</script>

<style scoped>
.container {
  height: 100vh;
  overflow-y: scroll;
}

.text-hex {
  font-size: 7rem;
}

.height100 {
  height: 100vh
}

.restart-button {
  position: absolute;
  bottom: 40px;
}

</style>
