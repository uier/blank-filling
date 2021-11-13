<template>
  <div class="w-full max-w-sm mx-auto">
    <div
      :class="['flex items-center border-b py-2', error ? 'border-red-700' : 'border-teal-500']">
      <input
        v-model="text"
        ref="input"
        :class="['appearance-none bg-transparent border-none w-full mr-3 py-1 px-2 leading-tight focus:outline-none text-center text-xl', error ? 'text-red-700' : 'text-gray-700']"
        type="text"
        placeholder="type here"
        @keyup.enter.capture="$emit('submit', text, () => text = '')"
      >
    </div>
    <div class="mt-5 text-gray-500">Press enter to submit.</div>
    <div class="mt-5 text-gray-500">Submit "idk" to reveal answer.</div>
  </div>
</template>

<script>
export default {
  props: ['error'],
  data: () => ({ text: '' }),
  mounted() {
    this.$refs.input.focus()
  },
}
</script>
