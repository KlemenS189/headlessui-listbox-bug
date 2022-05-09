<template>
  <div>
    {{ modelValue }}
    <Listbox v-model="modelValue" @update:modelValue="emitChange">
      <div>
        <ListboxButton
            class="w-full py-2 pl-3 pr-10 text-left bg-white rounded-lg shadow-md cursor-default focus:outline-none focus-visible:ring-2 focus-visible:ring-opacity-75 focus-visible:ring-white focus-visible:ring-offset-orange-300 focus-visible:ring-offset-2 focus-visible:border-indigo-500 sm:text-sm h-10 border-main-default border-2"
        >
          <span class="block truncate">{{ getCurrentTextValue }}</span>
          <span
              class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none"
          >
            <SelectorIcon class="w-5 h-5 text-gray-400" aria-hidden="true" />
          </span>
        </ListboxButton>

        <transition
            leave-active-class="transition duration-100 ease-in"
            leave-from-class="opacity-100"
            leave-to-class="opacity-0"
        >
          <ListboxOptions
              class="absolute w-full py-1 mt-1 overflow-auto text-base bg-white rounded-md shadow-lg max-h-60 ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm z-10"
          >
            <ListboxOption
                v-slot="{ active, selected }"
                v-for="option in options"
                :key="option.value"
                :value="option.value"
                as="template"
            >
              <li
                  :class="[
                  active
                    ? 'text-main-default bg-lilac-default'
                    : 'text-gray-900',
                  'cursor-default select-none relative py-2 pl-10 pr-4',
                ]"
              >
                <span
                    :class="[
                    selected ? 'font-medium' : 'font-normal',
                    'block truncate',
                  ]"
                >{{ option.text }}</span
                >
              </li>
            </ListboxOption>
          </ListboxOptions>
        </transition>
      </div>
    </Listbox>
  </div>
</template>

<script setup lang="ts">
import { computed, PropType } from 'vue'
import {
  Listbox,
  ListboxButton,
  ListboxOption,
  ListboxOptions,
} from '@headlessui/vue'
import { CheckIcon, SelectorIcon } from '@heroicons/vue/solid'
import { get } from 'lodash'

interface option {
  value: string
  text: string
}

const props = defineProps({
  modelValue: {
    type: String,
  },
  options: {
    type: Object as PropType<Array<option>>,
    default: () => [],
  },
})
const emits = defineEmits<{
  (e: 'update:modelValue', v: any): void
  (e: 'changed'): void
}>()

const emitChange = () => {
  emits('update:modelValue', props.modelValue)
  emits('changed')
}

const getCurrentTextValue = computed((): string => {
  const map: { [key: string]: string } = {}
  props.options?.forEach((val: option) => {
    map[val.value] = val.text
  })
  return get(map, props.modelValue)
})
</script>
