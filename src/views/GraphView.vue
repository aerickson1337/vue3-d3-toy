<script lang="ts">
import LineGraph from '../components/LineGraph.vue';
import { ref, computed } from 'vue';
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
    let datasetSize: Ref<number> = ref(1)
    const largeData: readonly number[] = Object.freeze(generateDataset(10000000))

    function updateDataSet() {
      data.value = data.value.concat(generateDataset(datasetSize.value))
    }

    return {
      data,
      datasetSize,
      generateDataset,
      updateDataSet,
      largeData,
    }
  }
}
</script>

<template>
  <div class="about">
    <div class="controls">
      <button class="button" @click="updateDataSet()">
        add
      </button>
      <input type="number" class="number-input" v-model.number="datasetSize">
      new datapoints
      <button class="button" @click="data = []">reset</button>
      {{data.length}}
    </div>
    <LineGraph :data="largeData" />
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    align-items: center;
    display: grid;
  }
}

.controls {
  grid-row: 1 / span 1;
}

.number-input {
  max-width: 50px;
  color: var(--color-text);
  background-color: var(--color-background);
  border-style: solid;
  border-width: 1px;
  border-color: grey;
}
</style>