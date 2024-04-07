<script setup>
import Synchronous from './components/Synchronous.vue';
import Asynchronous from './components/Asynchronous.vue';
import countries from './data/countries.json';
import { computed, ref } from 'vue'

const country = ref('')
const country1 = ref('')
</script>

<template>
  <div class="w-screen h-screen bg-gray-200 flex justify-center items-center">
      <div class="w-1/3">
        <Synchronous :source="countries" v-model="country" />
        <Asynchronous :source="countries" v-model="country1" />
      </div>
  </div>
</template>

