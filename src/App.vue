<template>
  <div>
    <div id="app"></div>
    <svg width="660" height="220" @click="handleClick($event)">
      <defs>
        <linearGradient
          id="linear"
          :x1="percentage(point1)"
          :y1="percentage(point1)"
          :x2="percentage(point2)"
          :y2="percentage(point2)"
        >
          <stop offset="0%" stop-color="#05a" />
          <stop offset="100%" stop-color="#0a5" />
        </linearGradient>
      </defs>

      <polyline :points="polygonPoints" fill="url(#linear)" />
    </svg>
  </div>
</template>

<script>
export default {
  data() {
    return {
      point1: { x: 0, y: 0 },
      point2: { x: 600, y: 100 },
      polygon: {
        brush: { size: 5, color: "#ff0000" },
        points: [
          [0, 200],
          [500, 0],
          [500, 200],
          [100, 50],
        ],
      },
    };
  },
  computed: {
    polygonPoints() {
      return this.polygon.points.map((point) => point.join(",")).join(" ");
    },
  },
  methods: {
    percentage(point) {
      return {
        x: (660 / point.x) * 100 + "%",
        y: (220 / point.x) * 100 + "%",
      };
    },
    handleClick(e) {
      console.log("handleClick", e);
      this.point1 = { x: e.clientX, y: e.clientY };
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
