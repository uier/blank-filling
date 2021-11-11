<template>
  <h1 class="text-3xl">みんなの日本語</h1>
  <div class="mt-20">
    <div class="text-2xl">{{ data[index].Q }}</div>
  </div>
  <Input class="mt-16" :error="error" @submit="submit" />
  <div class="mt-3" v-show="cnt > 2">
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
    const cnt = ref(0)
    const submit = (answer, cb) => {
      if ( data[index.value].A.includes(answer) ) {
        cnt.value = 0
        cb()
        index.value = genIndex()
      } else {
        error.value = true
        cnt.value += 1
        setTimeout(() => error.value = false, 1500)
      }
    }
    return { index, data, submit, error, cnt }
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
