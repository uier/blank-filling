<template>
  <h1 class="text-3xl">みんなの日本語</h1>
  <div class="mt-20">
    <div class="text-2xl">{{ data[index].Q }}</div>
  </div>
  <Input class="mt-16" :error="error" @submit="submit" />
  <div class="mt-3" v-show="reveal">
    {{ data[index].A.join(', ') }}
  </div>
  <div class="mt-20">
    <div v-for="c in chapters" :key="c">
      <label class="inline-flex items-center">
        <input v-model="selectedChapters" :value="c" type="checkbox" class="form-checkbox" />
        <span class="ml-2">{{ c }}</span>
      </label>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'
import Input from './components/Input.vue'
import { data as vocabulary } from './data.json'
/**
 * "data": {
 *   "chapter1": [
 *     { "Q": "q1", "A": ["a1", "a2"] },
 *     { "Q": "q2", "A": ["a1", "a2"] }
 *   ],
 *   "chapter2": [
 *     { "Q": "q1", "A": ["a1", "a2"] },
 *     { "Q": "q2", "A": ["a1", "a2"] }
 *   ],
 *   ...
 * }
 */

export default {
  components: { Input },
  setup() {
    // Extract all chapters, and render checkboxes to let user select
    const chapters = Object.keys(vocabulary)
    const selectedChapters = ref(chapters)
    // Compute the problemsets from selected chapters, data is an object array
    // data: [{ "Q": "q1", "A": ["a1", "a2"] }, { "Q": "q1", "A": ["a1", "a2"] }, ...]
    const data = computed(() => {
      return chapters.filter(k => selectedChapters.value.includes(k)).map(c => vocabulary[c]).reduce((a, b) => [...a, ...b], [])
    })
    // Compute the length to generate index for drawing the next question
    const len = computed(() => data.value.length)
    const genIndex = (i = -1) => {
      let candidate = Math.floor(Math.random() * len.value)
      while (candidate === i) {
        candidate = Math.floor(Math.random() * len.value)
      }
      return candidate
    }
    const index = ref(genIndex())
    // Note that re-generate index is nessesary when len is mutated, that is, problemsets has changed
    // Otherwise index may out of the range of new problemsets' length
    watch(len, () => {
      index.value = genIndex()
    })
    // Show error state when submitted answer is incorrect, and recovery after 1 sec.
    // Key in 'idk' will reveal answer, note that reveal should recovery on next normal submission
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
      if ( data.value[index.value].A.includes(answer) ) {
        cb()
        index.value = genIndex()
      } else {
        error.value = true
        setTimeout(() => error.value = false, 1000)
      }
    }
    return { index, chapters, selectedChapters, data, submit, error, reveal }
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
