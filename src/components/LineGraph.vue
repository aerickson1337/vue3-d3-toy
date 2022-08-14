<script lang="ts">
import * as d3 from "d3";
import { computed, defineProps, ref } from 'vue';
import type { ComputedRef, PropType, Ref } from "vue";
import type { Line, ScaleLinear, Numeric, Path, path } from "d3"

export default {
  name: 'LineChart',
  props: {
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
  },
  setup(props: any) {
    const padding: Ref<number> = ref(60)

    const rangeX: ComputedRef<number[]> = computed(() => {
      const paddedWidth: number = (props.width - padding.value)
      return [0, paddedWidth];
    })

    const rangeY: ComputedRef<number[]> = computed(() => {
      const paddedHeight: number = (props.height - padding.value)
      return [paddedHeight, 0];
    })

    const path: ComputedRef<Line<[number, number]>> = computed(() => {
      const xScale: ScaleLinear<number, number, never> = d3.scaleLinear().range(rangeX.value);
      const yScale: ScaleLinear<number, number, never> = d3.scaleLinear().range(rangeY.value);
      
      d3.axisLeft(xScale);
      d3.axisTop(yScale);

      const [xMin, xMax] = d3.extent(props.data, (_, i) => i) || [0, 0]
      if (xMin != undefined || xMax != undefined) {
        xScale.domain([xMin, xMax]);
      }

      const yMax: number = d3.max<number>(props.data) as number
      yScale.domain([0, yMax]);
      return d3.line()
        .x((d, i) => xScale(i))
        .y(d => yScale(d));
    })

    const line: ComputedRef<Path> = computed(() => {
      return path.value(props.data);
    })

    const viewBox: ComputedRef<string> = computed(() => {
      return `0 0 ${props.width} ${props.height}`;
    })

    return {
      rangeX,
      rangeY,
      path,
      line,
      viewBox,
      padding
    }
  }
};
</script>

<template>
  <div>
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
  stroke-width: 0.05vh + 0.05vw;
}
</style>