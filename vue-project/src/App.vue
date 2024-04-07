<script setup>
import Autocomplete from './components/Autocomplete.vue';
import Autocomplete1 from './components/Autocomplete1.vue';
import countries from './data/countries.json';
import { computed, ref } from 'vue'

const country = ref('')
const country1 = ref('')
</script>

<template>
  <div class="w-screen h-screen bg-gray-200 flex justify-center items-center">
      <div class="w-1/3">
        <Autocomplete :source="countries" v-model="country" />
        <Autocomplete1 :source="countries" v-model="country1" />
      </div>
  </div>
</template>

