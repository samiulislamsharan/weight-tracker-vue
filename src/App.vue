<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([])

const weighChartEl = ref(null)

const weightCart = shallowRef(null)

const weightInput = ref(60.0)

const currentWeight = computed(() => {
  return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime()
  })
}

watch(weights, newWeights => {
  const ws = [...newWeights]

  if (weightCart.value) {
    weightCart.value.data.labels = ws
      .sort((a, b) => a.date - b.date)
      .map(w => new Date(w.date).toLocaleDateString())

    weightCart.value.data.datasets[0].data = ws
      .sort((a, b) => a.date - b.date)
      .map(w => w.weight)

    weightCart.value.update()
  }

  nextTick(() => {
    // console.log(weighChartEl)

    weightCart.value = new Chart(weighChartEl.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: ws
          .sort((a, b) => a.date - b.date)
          .map(w => new Date(w.date).toLocaleDateString()),
        datasets: [
          {
            label: 'Weight',
            data: ws
              .sort((a, b) => a.date - b.date)
              .map(w => w.weight),
            backgroundColor: 'rgba(255, 99, 132, 0.2)',
            borderColor: 'rgba(255, 99, 132, 1)',
            borderWidth: 1,
            fill: true
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
      }
    })
  })

}, { deep: true })
</script>

<template>
  <main>
    <h1>Weight Tracker</h1>

    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current weight (KG)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput">
      <input type="submit" value="Add Weight">
    </form>

    <div v-if="weights && weights.length > 0">
      <h2>Last 7 days</h2>

      <div class="canvas-box">
        <canvas ref="weighChartEl"></canvas>
      </div>
    </div>

    <div class="weight-history">
      <h2>Weight History</h2>
      <ul>
        <li v-for="weight in weights">
          <span>{{ weight.weight }} kg </span>
          <small>{{ new Date(weight.date).toLocaleDateString() }}</small>
        </li>
      </ul>
    </div>

  </main>
</template>

<style></style>
