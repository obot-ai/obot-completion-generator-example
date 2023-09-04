<script setup lang="ts">
import { ref } from 'vue'
import { Fetcher, Generator, MatchedResultData } from "@obot-ai/completion-generator"

const input = ref('')
const API_KEY = import.meta.env.VITE_INPUT_COMPLETION_KEY
const generator = new Generator()
const fetcher = new Fetcher({
  apiKey: API_KEY,
  getEndpoint(locale) {
    return `${import.meta.env.VITE_INPUT_COMPLETION_HOST}/input_completion/${locale}/`
  },
  handleResponse(data) {
    return data.user_says
  }
})
fetcher.fetch("ja").then(data => {
  generator.loadData("ja", data)
})

const completions = ref<MatchedResultData[]>([])

let timer: number | undefined
const onInput = () => {
  window.clearTimeout(timer)

  window.setTimeout(() => {
    completions.value = generator.generateCompletions(input.value, "ja")
  }, 300)
}

</script>

<template>
  <input type="text" v-model="input" @input="onInput"/>
  <hr>
  <template v-for="completion in completions">
    <p>{{ completion.text }}</p>
  </template>
</template>
