<template>
  <main class="max-w-lg mx-auto py-20">
    <div class="flex items-center gap-4">
      <div class="bg-gray-300 rounded-full w-14 h-14"></div>
      <NumberInput
        v-model="student.age"
        input-id="hours-old"
        :label="`${student.name} is`"
        description="Student's age in hours"
      />
      <p class="text-gray-700">hours old</p>
    </div>
    <div class="flex items-center gap-4">
      <div class="bg-gray-300 rounded-full w-14 h-14"></div>
      <NumberInput
        v-model="student.age"
        input-id="hours-old"
        :label="`${student.name} is`"
        description="Student's age in hours"
        disabled
      />
      <span class="text-gray-700">hours old</span>
    </div>
  </main>
</template>

<script setup lang="ts">
import { reactive } from 'vue'
import NumberInput from '@/NumberInput.vue'

const student = reactive<{
  name: string
  age: string | null
  image: string | null
}>({
  name: 'Hugh',
  age: null,
  image: null,
})
</script>

<style></style>
