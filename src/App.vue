<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])

const weighChartE1 = ref(null)

const weightCart = shallowRef(null)

const weightInput = ref(60.0)

const currentWeight = computed(() => {
  return weight.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime()
  })
}
</script>

<template>
  <h1>Hello Vue</h1>
</template>

<style></style>
