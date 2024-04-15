<script setup lang="ts">
import { VICTORY_MESSAGE, DEFEAT_MESSAGE, WORD_SIZE } from '@/settings'
import englishWords from "@/englishWordsWith5Letters.json"
import { ref } from 'vue';
import { computed, triggerRef } from '@vue/reactivity';

const emit = defineEmits<{
  "guess-submitted": [guess: string]
}>();
const guessInProgress = ref<string|null>(null)

const formattedGuessInProgress = computed<string>({
  get() {
    return guessInProgress.value ?? ""
  },
  set(rawValue: string) {
    guessInProgress.value = null
    const newValue = rawValue.slice(0, WORD_SIZE).toUpperCase().replace(/[^A-Z]+/gi, "")
    guessInProgress.value = newValue
    triggerRef(formattedGuessInProgress); // force update, computed only trigger if its computed value has changed from the previous one
  }
})

const onSubmit = () => {
  if(!englishWords.includes(formattedGuessInProgress.value)) {
    return
  }

  emit("guess-submitted", formattedGuessInProgress.value)
}
</script>

<template>
  <input v-model="formattedGuessInProgress"
    :maxlength="WORD_SIZE"
    type="text" 
    @keydown.enter="onSubmit"
  >
</template>

