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

  .table-container {
    margin: 2em 0;
    display: flex;
    overflow-x: scroll;
    justify-content: space-evenly;
  }

  .charts {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .chart-container {
    padding: 15px;
    border: 1px solid rgba(0, 0, 0, 0.0625);
    background-color: #fff;
    width: 48%;
  }

  @media (max-width: 992px) {
    .chart-container {
      width: 98%;
      margin: 0;
    }
    .chart-container + .chart-container {
      margin-top: 1em;
    }
  }
</style>

<script>
  import { onMount, onDestroy } from "svelte";
  import Chart from "chart.js";
  import { TableSort } from "svelte-tablesort";
  import Box from "../shared/components/Box.svelte";
  import PageLoader from "../shared/components/PageLoader.svelte";

  let currentData;
  let previousData;
  let diffData;
  let confirmedChart;
  let localVsForeignChart;
  let tableData;
  let isMobile = false;
  let chartTimeOut;
  let loading = false;

  function getDiff(current, prev) {
    return {
      confirmed: current.summary.total - prev.summary.total,
      recovered: current.summary.discharged - prev.summary.discharged,
      deaths: current.summary.deaths - prev.summary.deaths,
      active: current.summary.active - prev.summary.active
    };
  }

  function getTableData(rawData) {
    const dataArray = rawData.map(ele => {
      ele.total = ele.confirmedCasesIndian + ele.confirmedCasesForeign;
      ele.active = ele.total - (ele.discharged + ele.deaths);
      return ele;
    });
    return dataArray;
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
    loading = true;
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
          tableData = getTableData(currentData.regional);
          chartTimeOut = setTimeout(() => {
            createConfirmedChart(res.data);
            createLocalVsForeign(res.data);
          }, 0);
        }
        loading = false;
      });
  }

  onMount(() => {
    getData();
    isMobile = window.innerWidth < 560;
  });
  onDestroy(() => {
    clearTimeout(chartTimeOut);
  });
</script>

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
  <div class="table-container">
    {#if isMobile}
      <TableSort items="{tableData}">
        <tr slot="thead">
          <th data-sort="loc">State / UT</th>
          <th data-sort="total" data-sort-initial="descending">CNFMRD</th>
          <th data-sort="active">ACTV</th>
          <th data-sort="discharged">RCVRD</th>
          <th data-sort="deaths">DCSD</th>
        </tr>
        <tr slot="tbody" let:item="{data}">
          <td class="state">{data.loc}</td>
          <td>{data.total}</td>
          <td>{data.active}</td>
          <td>{data.discharged}</td>
          <td>{data.deaths}</td>
        </tr>
      </TableSort>
    {:else}
      <TableSort items="{tableData}">
        <tr slot="thead">
          <th data-sort="loc">State / UT</th>
          <th data-sort="total" data-sort-initial="descending">Confirmed</th>
          <th data-sort="active">Active</th>
          <th data-sort="discharged">Recovered</th>
          <th data-sort="deaths">Dealths</th>
        </tr>
        <tr slot="tbody" let:item="{data}">
          <td class="state">{data.loc}</td>
          <td>{data.total}</td>
          <td>{data.active}</td>
          <td>{data.discharged}</td>
          <td>{data.deaths}</td>
        </tr>
      </TableSort>
    {/if}
  </div>
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
{:else if loading}
  <PageLoader />
{/if}
