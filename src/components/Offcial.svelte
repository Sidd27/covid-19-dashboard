<style>
  .main-data-container {
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
  }

  h3.chart-title {
    text-align: center;
    margin-bottom: 20px;
  }

  .charts {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .chart-container {
    margin: 2em auto;
    padding: 15px;
    border: 1px solid rgba(0, 0, 0, 0.0625);
    background-color: #fff;
    width: 48%;
  }

  @media (max-width: 992px) {
    .chart-container {
      width: 98%;
      margin: 10px 0 0;
    }
  }
</style>

<script>
  import { onMount } from "svelte";
  import Chart from "chart.js";
  import Box from "../shared/components/Box.svelte";

  let currentData;
  let previousData;
  let diffData;
  let confirmedChart;
  let localVsForeignChart;

  function getDiff(current, prev) {
    return {
      confirmed: current.summary.total - prev.summary.total,
      recovered: current.summary.discharged - prev.summary.discharged,
      deaths: current.summary.deaths - prev.summary.deaths,
      active: current.summary.active - prev.summary.active
    };
  }

  function getActive(data) {
    return data.summary.total - (data.summary.discharged + data.summary.deaths);
  }

  function getConfirmedChartData(data) {
    const labelArray = [];
    const dataSet = [];
    data.forEach(element => {
      const date = new Date(element.day);

      labelArray.push(`${date.getDate()}/${date.getMonth() + 1}`);
      dataSet.push(element.summary.total);
    });
    return {
      labelArray,
      dataSet
    };
  }

  function createConfirmedChart(rawData) {
    const ctx = confirmedChart.getContext("2d");
    const confirmedData = getConfirmedChartData(rawData);
    const chart = new Chart(ctx, {
      type: "line",
      data: {
        labels: confirmedData.labelArray,
        datasets: [
          {
            borderColor: "rgb(33,150,243)",
            backgroundColor: "rgb(33,150,243)",
            data: confirmedData.dataSet
          }
        ]
      },
      options: {
        legend: { display: false }
      }
    });
  }

  function getLocVsFonChartData(data) {
    const labelArray = [];
    const LocalDataSet = [];
    const ForeignDataSet = [];
    data.forEach(item => {
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

  function createLocalVsForeign(rawData) {
    const ctx = localVsForeignChart.getContext("2d");
    const data = getLocVsFonChartData(rawData);
    const chart = new Chart(ctx, {
      type: "line",
      data: {
        labels: data.labelArray,
        datasets: [
          {
            label: "Indian",
            fill: false,
            borderColor: "rgb(216,67,21)",
            data: data.LocalDataSet
          },
          {
            label: "Foreigner",
            fill: false,
            borderColor: "rgb(1,87,155)",
            data: data.ForeignDataSet
          }
        ]
      },
      options: {}
    });
  }

  async function getData() {
    await fetch(`https://api.rootnet.in/covid19-in/stats/daily`)
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          const dataLen = res.data.length;
          currentData = res.data[dataLen - 1];
          currentData.summary.active = getActive(currentData);
          previousData = res.data[dataLen - 2];
          previousData.summary.active = getActive(previousData);
          diffData = getDiff(currentData, previousData);
          createConfirmedChart(res.data);
          createLocalVsForeign(res.data);
        }
      });
  }

  onMount(() => {
    getData();
  });
</script>

<svelte:head>
  <title>Covid19 Status</title>
</svelte:head>
{#if currentData}
  <div class="main-data-container">
    <Box
      label="Total Confirmed"
      count="{currentData.summary.total}"
      type="confirmed"
      diff="{diffData.confirmed}"
    />
    <Box
      label="Total Active"
      count="{currentData.summary.active}"
      type="active"
      diff="{diffData.active}"
    />
    <Box
      label="Total Recoverd"
      count="{currentData.summary.discharged}"
      type="recovered"
      diff="{diffData.recovered}"
    />
    <Box
      label="Total Deaths"
      count="{currentData.summary.deaths}"
      type="deaths"
      diff="{diffData.deaths}"
    />
  </div>
{/if}
<div class="charts">
  <div class="chart-container">
    <h3 class="chart-title">Total Confirmed Case</h3>
    <canvas bind:this="{confirmedChart}"></canvas>
  </div>
  <div class="chart-container">
    <h3 class="chart-title">Indian Vs Foreigner</h3>
    <canvas bind:this="{localVsForeignChart}"></canvas>
  </div>
</div>
