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
            backgroundColor: 'rgba(37, 107, 76, 0.3)',
            borderColor: 'rgba(65, 184, 131, 1)',
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
      <span id="current-weight">{{ currentWeight.weight }}</span>
      <small>Current weight (KG)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput">
      <input type="submit" value="Add Weight">
    </form>

    <div v-if="weights && weights.length > 0" id="weight-chart">
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
}

body {
  background-color: #eee;
}

main {
  padding: 1.5rem;
  min-width: 400px;
  width: 100%;
}

h1 {
  font-size: 2em;
  text-align: center;
  margin-bottom: 2rem;
}

h2 {
  margin-bottom: 1rem;
  color: #888;
  font-weight: 400;
}

.current {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  width: 200px;
  height: 200px;

  text-align: center;
  background-color: white;
  border-radius: 999px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: 5px solid #41b883;

  margin: 0 auto 2rem;
}

.current span {
  display: block;
  font-size: 2em;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.current small {
  color: #888;
  font-style: italic;
}

form {
  display: flex;
  margin-bottom: 2rem;
  border: 1px solid #AAA;
  border-radius: 0.5rem;
  overflow: hidden;
  transition: 200ms linear;
}

form:focus-within,
form:hover {
  border-color: #41b883;
  border-width: 2px;
}

form input[type="number"] {
  appearance: none;
  outline: none;
  border: none;
  background-color: white;

  flex: 1 1 0%;
  padding: 1rem 1.5rem;
  font-size: 1.25rem;
}

form input[type="submit"] {
  appearance: none;
  outline: none;
  border: none;
  cursor: pointer;
  background-color: #41b883;

  padding: 0.5rem 1rem;

  color: white;
  font-size: 1.25rem;
  font-weight: 700;
  transition: 200ms linear;
  border-left: 3px solid transparent;
}

form input[type="submit"]:hover {
  background-color: white;
  color: #41b883;
  border-left-color: #41b883;
}

#weight-chart {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.canvas-box {
  width: 100%;
  max-width: 720px;
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.weight-history ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.weight-history ul li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  cursor: pointer;
}

.weight-history ul li:nth-child(even) {
  background-color: #dfdfdf;
}

.weight-history ul li:hover {
  background-color: #f8f8f8;
}

.weight-history ul li:last-of-type {
  border-bottom: none;
}

.weight-history ul li span {
  display: block;
  font-size: 1.25rem;
  font-weight: 700;
  margin-right: 1rem;
}

.weight-history ul li small {
  color: #888;
  font-style: italic;
}
</style>
