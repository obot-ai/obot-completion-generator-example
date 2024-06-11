<script setup lang="ts">
import { ref } from 'vue'
import type { CompletionMatcherProperties, MatchedResultData, LocaleDataItem } from "@obot-ai/completion-generator"
import { Fetcher, Generator, KeywordForwardMatcher } from "@obot-ai/completion-generator"

const input = ref('')
const API_KEY = import.meta.env.VITE_INPUT_COMPLETION_KEY
const matcherProperties: CompletionMatcherProperties = {
  keywordSeparator: ",",
  minKeywordLength: 2,
}
const matcher = new KeywordForwardMatcher(matcherProperties)
const generator = new Generator({
  matcher: matcher
})
const fetcher = new Fetcher({
  apiKey: API_KEY,
  getEndpoint(locale: string) {
    return `${import.meta.env.VITE_INPUT_COMPLETION_HOST}/input_completion/${locale}/`
  },
  handleResponse(data: any) {
    return data.user_says
  }
})
fetcher.fetch("ja").then((data: LocaleDataItem[]) => {
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
  <template v-for="completion in completions" :key="completion.text">
    <p>{{ completion.text }}</p>
  </template>
</template>
