<template>
  <div class="flex flex-col mb-4">
    <label
      :for="inputId"
      class="text-sm font-medium uppercase"
      :class="{ 'text-gray-700': !isFocused, 'text-indigo-500': isFocused }"
    >
      {{ label }}
    </label>
    <input
      :id="inputId"
      type="text"
      :value="formattedValue"
      @input="onInput"
      @focus="handleFocus"
      @blur="handleBlur"
      maxlength="12"
      :style="{ width: inputWidth }"
      :disabled="disabled"
      role="textbox"
      aria-live="polite"
      :aria-labelledby="inputId + '-label'"
      :aria-describedby="inputId + '-description'"
      class="p-2 border rounded focus:outline-none focus:ring-2 h-[44px]"
      :class="{
        'border-gray-300 focus:ring-indigo-500': !disabled,
        'bg-gray-100 border-gray-300 text-gray-400 cursor-not-allowed focus:ring-0':
          disabled,
      }"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref } from 'vue'

// Гораздо удобнее и "нативнее" работать в инпутах с шириной в ch, чем в пикселях,
// но т.к. у нас задана минимальная величина в пикселях, приходится немного изгаляться.
// Потенциально можно работать в пикселях, но для красивого и дженерного (для инпутов с разными шрифтами) эффекта нужно будет
// применять хаки с добавлением/удалением всяких <span> с нужным fontSize. А это куча перерисовок в DOM, что неоптимально.
// Еще я пробовал вариант с canvas))). Но для инпута оверкилл

// Стандартная величина для шрифтов
const PIXELS_FOR_CHARACTER = 8
const MIN_WIDTH_IN_PX = 72
const MAX_LENGTH_IN_CH = 20
const INPUT_PADDING = 4

const formatDigits = (value: string | null) => {
  return (value || '').replace(/(\d{1,3})(?=(\d{3})+(?!\d))/g, '$1 ')
}

export default defineComponent({
  name: 'NumberInput',
  props: {
    modelValue: {
      type: String,
      default: '',
    },
    label: {
      type: String,
      default: '',
    },
    description: {
      type: String,
      default: '',
    },
    inputId: {
      type: String,
      default: 'number-input',
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    const isFocused = ref(false)

    const formattedValue = computed(() => formatDigits(props.modelValue))

    const inputWidth = computed(() => {
      const widthInCh = formattedValue.value.length * PIXELS_FOR_CHARACTER + INPUT_PADDING
      return `${Math.max(MIN_WIDTH_IN_PX, widthInCh)}px`
    })

    const onInput = (event: Event) => {
      if (props.disabled) return

      const input = event.target as HTMLInputElement
      let rawValue = input.value.replace(/\D/g, '')

      if (rawValue.length > MAX_LENGTH_IN_CH) {
        rawValue = rawValue.slice(0, MAX_LENGTH_IN_CH)
      }

      input.value = formatDigits(rawValue)
      emit('update:modelValue', rawValue)
    }

    const handleFocus = () => (isFocused.value = true)
    const handleBlur = () => (isFocused.value = false)

    return {
      formattedValue,
      inputWidth,
      onInput,
      isFocused,
      handleFocus,
      handleBlur,
    }
  },
})
</script>

<style></style>
