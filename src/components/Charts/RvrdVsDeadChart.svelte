<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js';

  export let rawData;

  let chartElem;

  function getChartData() {
    const labelArray = [];
    const recovered = [];
    const death = [];
    rawData.forEach(item => {
      const date = new Date(item.day);
      labelArray.push(`${date.getDate()}/${date.getMonth() + 1}`);
      death.push(item.summary.deaths);
      recovered.push(item.summary.discharged);
    });
    return {
      labelArray,
      recovered,
      death
    };
  }

  function createChart() {
    const ctx = chartElem.getContext('2d');
    const data = getChartData();
    const chart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: data.labelArray,
        datasets: [
          {
            label: 'RECOVERED',
            fill: false,
            borderColor: 'rgb(76, 175, 80)',
            data: data.recovered
          },
          {
            label: 'DECEASED',
            fill: false,
            borderColor: 'rgb(244, 67, 54)',
            data: data.death
          }
        ]
      },
      options: {}
    });
  }

  //   Init Call
  onMount(() => {
    createChart();
  });
</script>

<div class="chart-container">
  <h3 class="chart-title">Recovered Vs Deceased</h3>
  <canvas bind:this="{chartElem}"></canvas>
</div>
