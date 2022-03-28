<template>
  <div class="container max-w-screen-sm mx-auto text-gray-800 py-10">
    <LogoIcon class="w-64 h-64 mx-auto mb-8" />
    <h1 class="text-2xl font-bold text-center mb-6">Розыгрыш призов от <span class="text-green-900">@besedki_krasnodar</span></h1>
    <p class="uppercase text-lg font-semibold flex items-center justify-center mb-4"><span class="text-4xl">🥇</span> аренда беседки НА ЦЕЛЫЙ ДЕНЬ</p>
    <p class="uppercase text-lg font-semibold flex items-center justify-center mb-4"><span class="text-4xl">🥈</span> скидка на любую беседку 50%</p>
    <h2 class="text-center text-xl font-semibold mb-3">Участники</h2>
    <textarea class="w-full h-32 bg-white rounded-lg p-3 mb-2" v-model="commentsText" />
    <transition
      enter-active-class="duration-700"
      enter-from-class="opacity-0 -mr-32"
      enter-to-class="opacity-100"
      leave-active-class="duration-700"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div v-if="people.length > 0">
        <div class="flex px-2 py-3 mb-2 rounded bg-blue-50 font-semibold text-lg">
          <div class="w-1/2">Участник</div>
          <div class="w-1/2">Комментарий</div>
        </div>
        <div v-for="(p, i) in people" class="flex px-2 py-3 mb-2 rounded" :class="i % 2 === 0 ? 'bg-green-100' : 'bg-blue-50'">
          <div class="w-1/2">{{ p }}</div>
          <div class="w-1/2">{{ comments[i] }}</div>
        </div>
      </div>
    </transition>
    <button
        v-if="people.length > 1 && !winnerFirst"
        @click="winFirst"
        type="button"
        class="w-full py-4 text-lg bg-green-400 rounded-lg text-white hover:bg-green-500 transition-colors uppercase mb-3 shadow"
    >
      🤩 Разыграть беседку 🤩
    </button>
    <button
        v-if="people.length > 1 && !winnerSecond"
        @click="winSecond"
        type="button"
        class="w-full py-4 text-lg bg-green-400 rounded-lg text-white hover:bg-green-500 transition-colors uppercase mb-3 shadow"
    >
      🔥 Разыграть скидку 🔥
    </button>
    <hr class="my-6">
    <p  v-if="winnerFirst || winnerSecond" class="flex justify-center items-center text-xl font-semibold mb-5"><span class="text-3xl mr-2">🎉</span> Поздравляем! </p>
    <transition
        enter-active-class="duration-500"
        enter-from-class="opacity-0"
        enter-to-class="opacity-100"
        leave-active-class="duration-75"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
    >
      <div v-show="winnerFirst" class="flex flex-col justify-center items-center bg-white rounded shadow p-4 mb-4">
        <p class="text-lg mb-2">Беседку бесплатно на целый день получает:</p>
        <p class="text-2xl flex items-center font-bold"><span class="text-4xl mr-2">🥇</span>{{ winnerFirst }}</p>
      </div>
    </transition>
    <transition
        enter-active-class="duration-500"
        enter-from-class="opacity-0"
        enter-to-class="opacity-100"
        leave-active-class="duration-300"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
    >
      <div v-show="winnerSecond" class="flex flex-col justify-center items-center bg-white rounded shadow p-4">
        <p class="text-lg mb-2">Скидку 50% на любую беседку получает:</p>
        <p class="text-2xl flex items-center font-bold"><span class="text-4xl mr-2">🥈</span>{{ winnerSecond }}</p>
      </div>
    </transition>
  </div>
</template>

<script>
import LogoIcon from '@/components/LogoIcon'

export default {
  data() {
    return {
      commentsText: '',
      randIndexFirstWinner: '',
      randIndexSecondWinner: ''
    }
  },
  components: {
    LogoIcon
  },
  computed: {
    dump() {
      const dataArray = typeof this.commentsText === 'string' && this.commentsText ? this.commentsText.split('\n') : []
      if (!dataArray?.length) {
        return
      }
      const userWithCommentArray = dataArray
        .filter(Boolean)
        .reduce((acc, val, idx) => idx % 2 !== 0
            ? (acc ? `${acc} ${val}` : `${val}`)
            : `${acc},${val}`)
        .split(',')
      const uniquerUserWithCommentArray = userWithCommentArray.filter((item, pos) => userWithCommentArray.indexOf(item) === pos)
      return uniquerUserWithCommentArray.join(' ').split(' ')
    },
    comments() {
      return this.dump?.length ? this.dump.filter((el, i) => i % 2 !== 0) : []
    },
    people() {
      if (this.dump?.length) {
        console.log(this.dump.filter((el, i) => i % 2 === 0 || i === 0))
      }
      return this.dump?.length ? this.dump.filter((el, i) => i % 2 === 0 || i === 0) : []
    },
    winnerFirst() {
      if (this.randIndexFirstWinner) {
        return this.people[this.randIndexFirstWinner]
      } else {
        return ''
      }
    },
    winnerSecond() {
      if (this.randIndexSecondWinner) {
        return this.people[this.randIndexSecondWinner]
      } else {
        return ''
      }
    }
  },
  methods: {
    getRandIndex() {
      const max = this.people.length
      return Math.floor(Math.random() * max)
    },
    winFirst() {
      const idx = this.getRandIndex()
      this.randIndexFirstWinner = idx
    },
    winSecond() {
      const idx = this.getRandIndex()
      if (this.people[idx] === this.winnerFirst) {
        this.winSecond()
        return null
      } else {
        this.randIndexSecondWinner = idx
      }
    }
  }
}
</script>