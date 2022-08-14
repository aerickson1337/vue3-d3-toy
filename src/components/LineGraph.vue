<script lang="ts">
import * as d3 from "d3";
import { computed, defineProps } from 'vue';
import type { ComputedRef, PropType } from "vue";
import type { Line, ScaleLinear, Numeric, Path, path } from "d3"

export default {
  name: 'LineChart',
  data() {
    return {
      padding: 60,
    };
  },
  setup() {
    const props = defineProps({
      data: {
        required: true,
        type: Array as PropType<number[]>,
      },
      width: {
        default: 500,
        type: Number as PropType<number>,
      },
      height: {
        default: 270,
        type: Number as PropType<number>,
      }
    })

    const rangeX: ComputedRef<number[]> = computed((width, padding) => {
      const paddedWidth: number = (width - padding)
      return [0, paddedWidth];
    })

    const rangeY: ComputedRef<number[]> = computed((height, padding) => {
      const paddedHeight: number = (height - padding)
      return [0, paddedHeight];
    })

    const path: ComputedRef<Line<[number, number]>> = computed((rangeX, rangeY, data) => {
      const xScale: ScaleLinear<number, number, never> = d3.scaleLinear().range(rangeX);
      const yScale: ScaleLinear<number, number, never> = d3.scaleLinear().range(rangeY);
      
      d3.axisLeft(xScale);
      d3.axisTop(yScale);

      const [xMin, xMax] = d3.extent(data, (_, i) => i) || [0, 0]
      if (xMin != undefined || xMax != undefined) {
        xScale.domain([xMin, xMax]);
      }

      const yMax: number = d3.max<number>(data) as number
      yScale.domain([0, yMax]);
      return d3.line()
        .x((d, i) => xScale(i))
        .y(d => yScale(d));
    })

    const line: ComputedRef<Path> = computed((data, path) => {
      return path(data);
    })

    const viewBox: ComputedRef<string> = computed((width, height) => {
      return `0 0 ${width} ${height}`;
    })

    return {
      rangeX,
      rangeY,
      path,
      line,
      viewBox,
    }
  }
};
</script>

<template>
  <div>
    <div>hello from linegraph!</div>
    {{path}}
    <svg
      class="line-chart"
      :viewBox="viewBox"
    >
      <g transform="translate(0, 10)">
        <path
          class="line"
          :d="line"
        />
      </g>
    </svg>
  </div>
</template>
<style scoped>
.line {
  fill: none;
  stroke: white;
  stroke-width: 1.5px;
}
</style>