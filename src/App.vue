<script setup>
import { ref } from 'vue'
import AutocompleteInput from './components/AutocompleteInput.vue'

const cars = ref([
  'Subaru',
  'Ford',
  'Lexus',,
  'Mitsubishi',
  'Audi',
  'Land Rover',
  'Ferrari',
  'Toyota',
  'Honda',
  'Nissan',
  'Mercury',
  'Lamborghini',
  'Kia',
  'Mazda',
  'Lincoln',
  'Chevrolet',
  'Mercedes-Benz',
  'Porsche',
  'Hyundai',
  'Suzuki',
  'Bentley',
  'Lotus',
  'BMW',
  'Rolls-Royce',
  'Maybach',
  'Tesla',
  'Maserati'
])

const inputValue = ref('')
</script>

<template>
  <AutocompleteInput :items="cars" v-model:input="inputValue" />
</template>

<style scoped>

</style>
