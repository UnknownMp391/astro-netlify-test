<template>{{ content }}</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const firstTime = 1000
const delTime = 80
const lowerTime = 100
const upperTime = 450
const keepTime = 1200

const words = [
  'FWP',
  'FTPod',
  'NeoGen',
  'CharGraph',
  'Roller'
]

enum Status {
  Keep,
  Typing,
  Deleting
}

const wordIndex = ref(0)
const charIndex = ref(words[0].length)
const state = ref(Status.Keep)
const content = ref(words[0])

function isUpperCase(char: string) {
  const code = char.charCodeAt(0);
  return code >= 65 && code <= 90;
}

function updateChar() {
  content.value = charIndex.value === 0
    ? ''
    : words[wordIndex.value].slice(0, charIndex.value)
  return charIndex.value === words[wordIndex.value].length
    ? null
    : isUpperCase(
      words[wordIndex.value].charAt(charIndex.value))
}

function tick() {
  switch (state.value) {
    case Status.Keep:
      if (charIndex.value === 0) {
        wordIndex.value++
        if (wordIndex.value === words.length) {
          wordIndex.value = 0
        }
        charIndex.value = 1
        state.value = Status.Typing
        setTimeout(tick, updateChar() ? upperTime : lowerTime)
      } else {
        charIndex.value -= 1
        state.value = Status.Deleting
        updateChar()
        setTimeout(tick, delTime)
      }
      break
    case Status.Typing:
      if (charIndex.value === words[wordIndex.value].length) {
        state.value = Status.Keep
        updateChar()
        setTimeout(tick, keepTime)
      } else {
        const upper = updateChar()
        charIndex.value++
        setTimeout(tick, upper ? upperTime : lowerTime)
      }
      break
    case Status.Deleting:
      charIndex.value -= 1
      if (charIndex.value === 0) {
        state.value = Status.Keep
        updateChar()
        setTimeout(tick, keepTime)
      } else {
        updateChar()
        setTimeout(tick, delTime)
      }
      break
  }
}

onMounted(() => setTimeout(tick, firstTime))
</script>
