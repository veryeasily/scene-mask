<template>
  <div>
    <div class="wrapper">
      <canvas ref="canvas" class="canvas" />
      <div class="screen" />
      <svg
        ref="svg"
        width="660"
        height="220"
        viewBox="0 0 660 220"
        @click="handleClick($event)"
        class="svg"
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
    </div>
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
    <button type="button" style="margin-top: 1rem;" @click="dumpSvgToBitmap">
      Click me to dump svg to bitmap
    </button>
    <div v-if="svgBitmap" class="bitmap">
      {{ svgBitmap }}
    </div>
  </div>
</template>

<script>
import Canvg from "canvg";

export default {
  data() {
    return {
      svgReady: false,
      targetPoint: "startPoint",
      svgBitmap: null,
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

    async dumpSvgToBitmap() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext("2d");
      const svgHtml = this.$refs.svg.outerHTML;
      const canvg = new Canvg.fromString(ctx, svgHtml);
      await canvg.render();
      this.svgBitmap = canvas.toDataURL("image/png");
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

<style scoped>
.wrapper {
  position: absolute;
  width: 660px;
  height: 220px;
  position: relative;
}

.svg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2;
}

.canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.screen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  background-color: white;
}

.bitmap {
  margin-top: 1rem;
  margin-left: 1rem;
  border: 2px solid #888;
  max-width: 500px;
  word-break: break-all;
  overflow-wrap: break-word;
}
</style>
