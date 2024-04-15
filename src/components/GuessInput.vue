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
  <ul class="word">
    <li v-for="(letter, index) in formattedGuessInProgress.padEnd(WORD_SIZE, ' ')"
      :key="`${letter}-${index}`"
      :data-letter="letter"
      class="letter"
      v-text="letter"
    />
  </ul>

  <input v-model="formattedGuessInProgress"
    :maxlength="WORD_SIZE"
    type="text" 
    autofocus
    @blur="({target}) => (target as HTMLInputElement).focus()"
    @keydown.enter="onSubmit"
  >
</template>

<style scoped>
input {
  position: absolute;
  opacity: 0;
}

.word {
  list-style: none;
  padding: 0;
  display: flex;
  gap: 0.25rem;
}

.letter {
  background-color: white;
  border: 1px solid hsl(0, 0%, 70%);
  width: 5rem;
  height: 5rem;
  height: 5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
  font-weight: bolder;
}

li:not([data-letter=" "]) {
  animation: pop 100ms;
}

@keyframes pop {
  0% {
    transform: scale(1);
  }
  
  50% {
    transform: scale(1.4);
  }
}
</style>
