<template>
<div class="page">
  <div class="controls">
    <div class="description">
      <div>根據<a :href="report.source" target="_blank">{{ report.sourceName }} {{ report.date }} {{ report.sourceType }}</a></div>
      <div>請描出 <span class="emphasis">{{ aircraft.type }}</span> 在地圖上的飛行路徑</div>
    </div>
    <div class="buttons">
      <button @click="zoom(1)">放大</button>
      <button @click="zoom(0)">原尺寸</button>
      <button @click="zoom(-1)">縮小</button>
      <button @click="save()">儲存</button>
      <button @click="undo()">復原</button>
      <button @click="clear()">清除</button>
    </div>
  </div>
  <div class="map" :style="{ width: `${w}px`, height: `${h}px` }">
    <img class="image" :src="report.map" :style="zoomStyle" />
    <svg class="canvas" :viewBox="`0 0 ${w} ${h}`" :style="zoomStyle" @click="clicked">
      <circle v-for="point of points" :cx="point.x" :cy="point.y" :r="r" />
      <polyline :points="points.map(p => `${p.x},${p.y}`).join(' ')" />
    </svg>
  </div>
</div>
</template>

<script>
import reports from '~/data/pla-warplane-incursion-reports.json'

export default {
  data() {
    const r = 0
    const report = reports[r]
    const a = Math.floor(Math.random() * Math.floor(report.aircrafts.length))
    const aircraft = report.aircrafts[a]
    return {
      step: 0,
      report,
      aircraft,
      z: 1.0,
      w: 960,
      h: 720,
      points: [],
      r: 2
    }
  },
  computed: {
    zoomStyle() {
      return {
        transform: `scale(${this.z})`
      }
    }
  },
  methods: {
    zoom(d) {
      if(d === 0) {
        this.z = 1.0
      } else {
        this.z += d / 5.0
      }
    },
    save() {
    },
    undo() {
      this.points.pop()
    },
    clear() {
      this.points = []
      this.z = 1.0
    },
    clicked(event) {
      let { top, left } = event.target.getBoundingClientRect()
      top += document.documentElement.scrollTop
      left += document.documentElement.scrollLeft
      const mx = (event.pageX - left) / this.z 
      const my = (event.pageY - top) / this.z
      console.log(mx, my, top, left)
      this.points.push({ x: mx, y: my })
    }
  }
}
</script>

<style lang="scss">
.page {
  position: relative;
  $offset-top: 0.75rem;
  > .controls {
    position: sticky;
    top: $offset-top;
    left: $offset-top;
    z-index: 1;
    display: inline-block;
    border: 0.25rem solid black;
    border-radius: 0.5rem;
    background: white;
    padding: 1rem;
    > .buttons {
      margin-top: 0.5rem;
    }
  }
  > .map {
    position: relative;
    margin-top: $offset-top;
    > .image {
      transform-origin: top left;
    }
    > .canvas {
      transform-origin: top left;
      position: absolute;
      top: 0;
      left: 0;
      > circle {
        stroke: none;
        fill: red;
      }
      > polyline {
        fill: none;
        stroke: red;
        stroke-width: 2px;
      }
    }
  }
  button {
    appearance: none;
    font-size: 0.875rem;
  }
}
</style>