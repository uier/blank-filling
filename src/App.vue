<template>
  <h1 class="text-3xl">みんなの日本語</h1>
  <div class="mt-20">
    <div class="text-2xl">{{ data[index].Q }}</div>
  </div>
  <Input class="mt-16" :error="error" @submit="submit" />
  <div class="mt-3" v-show="reveal">
    {{ data[index].A.join(', ') }}
  </div>
</template>

<script>
import { ref } from '@vue/reactivity'
import Input from './components/Input.vue'
import { data } from './data.json'

export default {
  components: { Input },
  setup() {
    const len = data.length
    const genIndex = (i = -1) => {
      let candidate = Math.floor(Math.random() * len)
      while (candidate === i) {
        candidate = Math.floor(Math.random() * len)
      }
      return candidate
    }
    const index = ref(genIndex())
    const error = ref(false)
    const reveal = ref(false)
    const submit = (answer, cb) => {
      if ( answer === '' ) return
      if ( answer === 'idk' ) {
        cb()
        reveal.value = true
        return
      }
      reveal.value = false
      if ( data[index.value].A.includes(answer) ) {
        cb()
        index.value = genIndex()
      } else {
        error.value = true
        setTimeout(() => error.value = false, 1000)
      }
    }
    return { index, data, submit, error, reveal }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
