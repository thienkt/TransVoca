<script setup lang="ts">
import { ref, computed } from 'vue';
import { Listbox, ListboxButton, ListboxOptions, ListboxOption } from '@headlessui/vue';

// Define language options
const languages = [
  { name: 'English', code: 'en' },
  { name: 'Vietnamese', code: 'vi' },
  { name: 'Spanish', code: 'es' },
  { name: 'French', code: 'fr' },
  { name: 'German', code: 'de' },
  { name: 'Japanese', code: 'ja' },
  { name: 'Chinese', code: 'zh' },
];

// Language selection
const sourceLang = ref(languages[0]);
const targetLang = ref(languages[1]);

// Text content
const sourceText = ref('');
const translatedText = computed(() => {
  // In a real app, this would call a translation API
  return sourceText.value ? `[Translation of: ${sourceText.value}]` : '';
});

// Speech state
const isSpeaking = ref(false);

// Swap languages
const swapLanguages = () => {
  const temp = sourceLang.value;
  sourceLang.value = targetLang.value;
  targetLang.value = temp;
};

// Play text (text-to-speech)
const speakText = (text: string, lang: { code: string }) => {
  // If speech synthesis is available, text exists, and not currently speaking
  if ('speechSynthesis' in window && text && !isSpeaking.value) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = lang.code;

    // Set speaking state to true
    isSpeaking.value = true;

    // Add event handlers to reset speaking state
    utterance.onend = () => {
      isSpeaking.value = false;
    };
    utterance.onerror = () => {
      isSpeaking.value = false;
    };

    // Start speaking
    window.speechSynthesis.speak(utterance);
  }
};
</script>

<template>
  <div class="w-[500px] bg-slate-50 p-4 rounded-xl">
    <!-- Title - reduced size and margin -->
    <h1 class="text-lg font-bold text-center mb-2 text-primary-700">TransVoca</h1>

    <div class="flex space-x-2">
      <!-- Source language panel -->
      <div class="flex-1 bg-white rounded-lg shadow-sm overflow-hidden">
        <!-- Language selector - reduced padding -->
        <Listbox v-model="sourceLang">
          <div class="relative">
            <ListboxButton
              class="relative w-full py-1.5 pl-3 pr-10 text-left cursor-pointer focus:outline-none"
            >
              <span class="block truncate font-medium">{{ sourceLang.name }}</span>
              <span class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none">
                <svg
                  aria-hidden="true"
                  class="h-4 w-4 text-gray-400"
                  fill="currentColor"
                  viewBox="0 0 20 20"
                >
                  <path
                    clip-rule="evenodd"
                    d="M10 3a.75.75 0 01.55.24l3.25 3.5a.75.75 0 11-1.1 1.02L10 4.852 7.3 7.76a.75.75 0 01-1.1-1.02l3.25-3.5A.75.75 0 0110 3z"
                    fill-rule="evenodd"
                  />
                </svg>
              </span>
            </ListboxButton>
            <div class="absolute z-10 w-full">
              <ListboxOptions
                class="absolute w-full py-1 mt-1 overflow-auto bg-white rounded-md shadow-lg max-h-60 ring-1 ring-black/5 focus:outline-none"
              >
                <ListboxOption
                  v-for="language in languages"
                  :key="language.code"
                  v-slot="{ active, selected }"
                  :value="language"
                >
                  <li
                    :class="[
                      active ? 'bg-primary-100 text-primary-900' : 'text-gray-900',
                      'cursor-pointer select-none relative py-2 pl-10 pr-4',
                    ]"
                  >
                    <span :class="[selected ? 'font-medium' : 'font-normal', 'block truncate']">
                      {{ language.name }}
                    </span>
                    <span
                      v-if="selected"
                      class="absolute inset-y-0 left-0 flex items-center pl-3 text-primary-600"
                    >
                      <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
                        <path
                          clip-rule="evenodd"
                          d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                          fill-rule="evenodd"
                        />
                      </svg>
                    </span>
                  </li>
                </ListboxOption>
              </ListboxOptions>
            </div>
          </div>
        </Listbox>

        <!-- Text input - increased rows -->
        <div class="relative">
          <textarea
            v-model="sourceText"
            class="w-full p-3 focus:outline-none resize-none"
            :placeholder="`Type in ${sourceLang.name}...`"
            rows="6"
          ></textarea>

          <!-- Speaker icon -->
          <button
            class="absolute bottom-2 right-2 p-1 rounded-full text-gray-400 hover:text-primary-500 hover:bg-gray-100"
            :class="{
              'opacity-50 cursor-not-allowed': !sourceText || isSpeaking,
              'text-primary-500': isSpeaking,
            }"
            :disabled="!sourceText || isSpeaking"
            @click="speakText(sourceText, sourceLang)"
          >
            <svg
              class="h-5 w-5"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                clip-rule="evenodd"
                d="M9.383 3.076A1 1 0 0110 4v12a1 1 0 01-1.707.707L4.586 13H2a1 1 0 01-1-1V8a1 1 0 011-1h2.586l3.707-3.707a1 1 0 011.09-.217zM14.657 2.929a1 1 0 011.414 0A9.972 9.972 0 0119 10a9.972 9.972 0 01-2.929 7.071a1 1 0 01-1.414-1.414A7.971 7.971 0 0017 10c0-2.21-.894-4.208-2.343-5.657a1 1 0 010-1.414zm-2.829 2.828a1 1 0 011.415 0A5.983 5.983 0 0115 10a5.984 5.984 0 01-1.757 4.243a1 1 0 01-1.415-1.415A3.984 3.984 0 0013 10a3.983 3.983 0 00-1.172-2.828a1 1 0 010-1.415z"
                fill-rule="evenodd"
              />
            </svg>
          </button>
        </div>
      </div>

      <!-- Swap button -->
      <div class="flex items-center">
        <button
          class="p-2 rounded-full bg-primary-100 text-primary-600 hover:bg-primary-200"
          @click="swapLanguages"
        >
          <svg
            class="h-5 w-5"
            fill="currentColor"
            viewBox="0 0 20 20"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M8 5a1 1 0 100 2h5.586l-1.293 1.293a1 1 0 001.414 1.414l3-3a1 1 0 000-1.414l-3-3a1 1 0 10-1.414 1.414L13.586 5H8zM12 15a1 1 0 100-2H6.414l1.293-1.293a1 1 0 10-1.414-1.414l-3 3a1 1 0 000 1.414l3 3a1 1 0 001.414-1.414L6.414 15H12z"
            />
          </svg>
        </button>
      </div>

      <!-- Target language panel -->
      <div class="flex-1 bg-white rounded-lg shadow-sm overflow-hidden">
        <!-- Language selector - reduced padding -->
        <Listbox v-model="targetLang">
          <div class="relative">
            <ListboxButton
              class="relative w-full py-1.5 pl-3 pr-10 text-left cursor-pointer focus:outline-none"
            >
              <span class="block truncate font-medium">{{ targetLang.name }}</span>
              <span class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none">
                <svg
                  aria-hidden="true"
                  class="h-4 w-4 text-gray-400"
                  fill="currentColor"
                  viewBox="0 0 20 20"
                >
                  <path
                    clip-rule="evenodd"
                    d="M10 3a.75.75 0 01.55.24l3.25 3.5a.75.75 0 11-1.1 1.02L10 4.852 7.3 7.76a.75.75 0 01-1.1-1.02l3.25-3.5A.75.75 0 0110 3z"
                    fill-rule="evenodd"
                  />
                </svg>
              </span>
            </ListboxButton>
            <div class="absolute z-10 w-full">
              <ListboxOptions
                class="absolute w-full py-1 mt-1 overflow-auto bg-white rounded-md shadow-lg max-h-60 ring-1 ring-black/5 focus:outline-none"
              >
                <ListboxOption
                  v-for="language in languages"
                  :key="language.code"
                  v-slot="{ active, selected }"
                  :value="language"
                >
                  <li
                    :class="[
                      active ? 'bg-primary-100 text-primary-900' : 'text-gray-900',
                      'cursor-pointer select-none relative py-2 pl-10 pr-4',
                    ]"
                  >
                    <span :class="[selected ? 'font-medium' : 'font-normal', 'block truncate']">
                      {{ language.name }}
                    </span>
                    <span
                      v-if="selected"
                      class="absolute inset-y-0 left-0 flex items-center pl-3 text-primary-600"
                    >
                      <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
                        <path
                          clip-rule="evenodd"
                          d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                          fill-rule="evenodd"
                        />
                      </svg>
                    </span>
                  </li>
                </ListboxOption>
              </ListboxOptions>
            </div>
          </div>
        </Listbox>

        <!-- Translated text output - increased min-height -->
        <div class="relative">
          <div
            class="w-full h-full p-3 min-h-[144px]"
            :class="{ 'text-gray-400': !translatedText }"
          >
            {{ translatedText || `Translation will appear here...` }}
          </div>

          <!-- Target language speaker button -->
          <button
            class="absolute bottom-2 right-2 p-1 rounded-full text-gray-400 hover:text-primary-500 hover:bg-gray-100"
            :class="{
              'opacity-50 cursor-not-allowed': !translatedText || isSpeaking,
              'text-primary-500': isSpeaking,
            }"
            :disabled="!translatedText || isSpeaking"
            @click="speakText(translatedText, targetLang)"
          >
            <svg
              class="h-5 w-5"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                clip-rule="evenodd"
                d="M9.383 3.076A1 1 0 0110 4v12a1 1 0 01-1.707.707L4.586 13H2a1 1 0 01-1-1V8a1 1 0 011-1h2.586l3.707-3.707a1 1 0 011.09-.217zM14.657 2.929a1 1 0 011.414 0A9.972 9.972 0 0119 10a9.972 9.972 0 01-2.929 7.071a1 1 0 01-1.414-1.414A7.971 7.971 0 0017 10c0-2.21-.894-4.208-2.343-5.657a1 1 0 010-1.414zm-2.829 2.828a1 1 0 011.415 0A5.983 5.983 0 0115 10a5.984 5.984 0 01-1.757 4.243a1 1 0 01-1.415-1.415A3.984 3.984 0 0013 10a3.983 3.983 0 00-1.172-2.828a1 1 0 010-1.415z"
                fill-rule="evenodd"
              />
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* We can remove unused styles and add any custom styles here */
</style>
