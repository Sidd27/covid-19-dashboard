<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js';

  export let rawData;

  let chartElem;

  function getChartData() {
    let male = 0;
    let female = 0;
    rawData.forEach(item => {
      if (item.gender === 'female') {
        female++;
      } else if (item.gender === 'male') {
        male++;
      }
    });
    return {
      male,
      female
    };
  }

  function createChart() {
    const ctx = chartElem.getContext('2d');
    const data = getChartData();
    const chart = new Chart(ctx, {
      type: 'doughnut',
      data: {
        labels: ['Males', 'Females'],
        datasets: [
          {
            data: [data.male, data.female],
            backgroundColor: ['rgb(137, 240, 255)', 'rgb(231, 100, 134)']
          }
        ]
      },
      options: {
        rotation: -Math.PI,
        circumference: Math.PI
      }
    });
  }

  //   Init Call
  onMount(() => {
    createChart();
  });
</script>

<div class="chart-container">
  <h3 class="chart-title">
    Male Vs Female Confrimed Cases
    <sup>**</sup>
  </h3>
  <canvas bind:this="{chartElem}"></canvas>
</div>
