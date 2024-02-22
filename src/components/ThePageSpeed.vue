<script>
export default {
  name: "pageSpeed",
  data() {
    return {
      performance: 0,
      temp: "",
      lighthouseMetrics: {},
    };
  },
  props: ["currentPageURL"],

  mounted() {
    const ref = this;
    function setUpQuery() {
      const api = "https://www.googleapis.com/pagespeedonline/v5/runPagespeed";
      const parameters = {
        url: encodeURIComponent(ref.currentPageURL),
      };
      let query = `${api}?`;
      for (let key in parameters) {
        query += `${key}=${parameters[key]}`;
      }
      return query;
    }

    const url = setUpQuery();
    fetch(url)
      .then((response) => {
        return response.json();
      })
      .then((json) => {
        const lighthouse = json.lighthouseResult;
        console.log(json.lighthouseResult.audits);
        ref.lighthouseMetrics = {
          "Largest Contentful Paint":
            lighthouse.audits["largest-contentful-paint"].displayValue,
          "First Input Delay":
            lighthouse.audits["max-potential-fid"].displayValue,
          "Cumulative Layout Shift": lighthouse.audits["cumulative-layout-shift"].displayValue,
          "First Contentful Paint": lighthouse.audits["first-contentful-paint"].displayValue,
          "First Meaningful Paint":
            lighthouse.audits["first-meaningful-paint"].displayValue,
        };
        ref.performance =
          +json.lighthouseResult.categories.performance.score * 100;
      });
  },
};
</script>

<template>
  <div
    v-if="performance"
    class="flex-wrapper d-flex justify-center flex-column align-center"
  >
    <div class="single-chart">
      <svg viewBox="0 0 36 36" class="circular-chart orange">
        <path
          class="circle-bg"
          d="M18 2.0845
          a 15.9155 15.9155 0 0 1 0 31.831
          a 15.9155 15.9155 0 0 1 0 -31.831"
        />
        <path
          class="circle"
          :stroke-dasharray="performance ? `${performance}, 100` : '0, 100'"
          d="M18 2.0845
          a 15.9155 15.9155 0 0 1 0 31.831
          a 15.9155 15.9155 0 0 1 0 -31.831"
        />
        <text x="18" y="20.35" class="percentage">{{ performance }}%</text>
      </svg>
    </div>
    <p>Производительность</p>
    <br>
    <div class="metrics">
      <div class="metrics-item" v-for="(value, name) in lighthouseMetrics">
      <p>{{ name }}: {{ value }}</p></div>
    </div>
  </div>
  <div v-else class="text-center">Загрузка...</div>
</template>

<style>
.flex-wrapper {
  display: flex;
  flex-flow: row nowrap;
}

.single-chart {
  width: 33%;
  justify-content: space-around;
}

.circular-chart {
  display: block;
  margin: 10px auto;
  max-width: 80%;
  max-height: 250px;
}

.circle-bg {
  fill: none;
  stroke: #eee;
  stroke-width: 3.8;
}

.circle {
  fill: none;
  stroke-width: 2.8;
  stroke-linecap: round;
  animation: progress 1s ease-out forwards;
}

@keyframes progress {
  0% {
    stroke-dasharray: 0 100;
  }
}

.circular-chart.orange .circle {
  stroke: #ff9f00;
}

.circular-chart.green .circle {
  stroke: #4cc790;
}

.circular-chart.blue .circle {
  stroke: #3c9ee5;
}

.percentage {
  fill: #666;
  font-family: sans-serif;
  font-size: 0.5em;
  text-anchor: middle;
}
</style>