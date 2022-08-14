<script lang="ts">
import LineGraph from '../components/LineGraph.vue';
import { ref } from 'vue';
import type { Ref } from 'vue';
import * as d3 from 'd3';

function generateDataset(datasetSize: number) {
  let dataset: Array<number> = [...Array(datasetSize)].map((i: number) => d3.randomUniform(1, 10)())
  return dataset
}

export default {
  components: { LineGraph },
  setup() {
    let data: Ref<number[]> = ref(generateDataset(100))

    // expose the state to the template
    return {
      data,
      generateDataset,
    }
  }
}
</script>

<template>
  <div class="about">
    <LineGraph :data="data" />
    <button @click="data.push(...generateDataset(1))">add new datapoint</button>
    <button @click="data = generateDataset(5)">reset</button>
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>