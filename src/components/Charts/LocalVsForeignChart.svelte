<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js';

  export let rawData;

  let chartElem;

  function getChartData() {
    const labelArray = [];
    const LocalDataSet = [];
    const ForeignDataSet = [];
    rawData.forEach(item => {
      const date = new Date(item.day);
      labelArray.push(`${date.getDate()}/${date.getMonth() + 1}`);
      ForeignDataSet.push(item.summary.confirmedCasesForeign);
      LocalDataSet.push(item.summary.confirmedCasesIndian);
    });
    return {
      labelArray,
      LocalDataSet,
      ForeignDataSet
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
            label: 'Indian',
            fill: false,
            borderColor: 'rgb(216,67,21)',
            data: data.LocalDataSet
          },
          {
            label: 'Foreigner',
            fill: false,
            borderColor: 'rgb(1,87,155)',
            data: data.ForeignDataSet
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
  <h3 class="chart-title">Indian Vs Foreigner</h3>
  <canvas bind:this="{chartElem}"></canvas>
</div>
