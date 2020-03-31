<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js';
  export let rawData;

  let chartElem;

  function getChartData() {
    const labelArray = [];
    const dataSet = [];
    rawData.forEach(element => {
      const date = new Date(element.day);

      labelArray.push(`${date.getDate()}/${date.getMonth() + 1}`);
      dataSet.push(element.summary.total);
    });
    return {
      labelArray,
      dataSet
    };
  }

  function createChart() {
    const ctx = chartElem.getContext('2d');
    const confirmedData = getChartData();
    const chart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: confirmedData.labelArray,
        datasets: [
          {
            borderColor: 'rgb(33,150,243)',
            backgroundColor: 'rgba(33,150,243, 0.2)',
            data: confirmedData.dataSet
          }
        ]
      },
      options: {
        tooltips: {
          mode: 'index',
          intersect: false
        },
        legend: { display: false }
      }
    });
  }
  //   Init Call
  onMount(() => {
    createChart();
  });
</script>

<div class="chart-container mt-container">
  <h3 class="chart-title">Total Confirmed Case</h3>
  <canvas bind:this="{chartElem}"></canvas>
</div>
