<template>
  <div>
    <div id="app"></div>
    <svg
      ref="svg"
      width="660"
      height="220"
      viewBox="0 0 660 220"
      @click="handleClick($event)"
    >
      <template v-if="svgReady">
        <defs>
          <linearGradient
            id="linear"
            :x1="percentage(points.startPoint).x"
            :y1="percentage(points.startPoint).y"
            :x2="percentage(points.endPoint).x"
            :y2="percentage(points.endPoint).y"
          >
            <stop offset="0%" stop-color="#05a" />
            <stop offset="100%" stop-color="#0a5" />
          </linearGradient>
        </defs>

        <polyline :points="polygonPoints" fill="url(#linear)" />
      </template>
    </svg>
    <button
      type="button"
      style="margin-top: 1rem;"
      @click="
        points.currentTarget =
          points.currentTarget === 'startPoint' ? 'endPoint' : 'startPoint'
      "
    >
      Click me to change the target point
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      svgReady: false,
      targetPoint: "startPoint",
      points: {
        currentTarget: "startPoint",
        startPoint: { x: 0, y: 0 },
        endPoint: { x: 600, y: 100 },
      },
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
  mounted() {
    this.svgReady = Boolean(this.$refs.svg);
  },
  updated() {
    this.svgReady = Boolean(this.$refs.svg);
  },
  methods: {
    percentage(point) {
      const temp = {
        x: (point.x / 660) * 100 + "%",
        y: (point.y / 220) * 100 + "%",
      };
      console.log("percentage", temp);
      return temp;
    },

    handleClick(e) {
      const bounds = this.$refs.svg.getBoundingClientRect();
      const point = {
        x: e.clientX - bounds.left,
        y: e.clientY - bounds.top,
      };
      console.log("handleClick", e, point);
      this.$set(this.points, this.points.currentTarget, point);
    },

    // NOTE: Not currently used but will probably eventually be useful. This
    // converts screen coordinates to SVG coordinates.
    //
    // svgPoint() {
    //   return this.$refs.svg.createSVGPoint();
    // },
    // // screenPoint needs to be an object with Number "x" and "y" properties.
    // toSvgPoint(screenPoint) {
    //   const svg = this.$refs.svg;

    //   const svgPoint = svg.createSVGPoint();
    //   svgPoint.x = screenPoint.x;
    //   svgPoint.y = screenPoint.y;

    //   const matrix = svg.getScreenCTM();
    //   const cursorpt = svgPoint.matrixTransform(matrix.inverse());

    //   console.log("(" + cursorpt.x + ", " + cursorpt.y + ")");
    //   return cursorpt;
    // },
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
