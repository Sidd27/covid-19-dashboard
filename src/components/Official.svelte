<style>
  .cards-container {
    width: 35%;
  }

  .card {
    background-color: #fff;
    border: 1px solid rgba(0, 0, 0, 0.0625);
    border-radius: 5px;
  }
  .card-title {
    background-color: #f5f5f5;
    color: #333;
    font-weight: 600;
    padding: 10px 20px;
  }

  .card-body {
    padding: 15px;
    text-align: center;
  }

  .card-body-title {
    font-size: 24px;
  }

  .card-content {
    display: flex;
    justify-content: space-between;
  }

  .card-item {
    font-size: 18px;
  }

  @media (max-width: 992px) {
    .cards-container {
      width: 100%;
    }
  }
</style>

<script>
  // 3rd Party Imports
  import { onMount } from 'svelte';
  import Chart from 'chart.js';
  import { TableSort } from 'svelte-tablesort';
  // Shared Resources
  import Box from '../shared/components/Box.svelte';
  import PageLoader from '../shared/components/PageLoader.svelte';
  import DashboardTitle from '../shared/components/DashboardTitle.svelte';
  import Table from '../shared/components/Table.svelte';
  // Chart Imports
  import RcvrdVsDeathChart from './Charts/RvrdVsDeadChart.svelte';
  import DailyChart from './Charts/DailyChart.svelte';

  let totalData;
  let currentData;
  let diffData;
  let tableData;
  let yesterdayTableData;
  let loading = false;
  let updatedDate;
  let totalOutcome;

  function getDiff(current, prev) {
    return {
      confirmed: current.summary.total - prev.summary.total,
      recovered: current.summary.discharged - prev.summary.discharged,
      deaths: current.summary.deaths - prev.summary.deaths,
      active: current.summary.active - prev.summary.active
    };
  }

  function calculatePercentage(num, total) {
    return ((num / total) * 100).toFixed(2);
  }

  function getTableData(rawData) {
    const dataArray = rawData.map(ele => {
      ele.confirmed = ele.confirmedCasesIndian + ele.confirmedCasesForeign;
      ele.active = ele.confirmed - (ele.discharged + ele.deaths);
      ele.state = ele.loc;
      ele.recovered = ele.discharged;
      return ele;
    });
    return dataArray;
  }

  function getActive(data) {
    return data.summary.total - (data.summary.discharged + data.summary.deaths);
  }

  async function getData() {
    loading = true;
    await fetch(`https://api.rootnet.in/covid19-in/stats/daily`)
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          totalData = res.data;

          const dataLen = totalData.length;
          currentData = totalData[dataLen - 1];
          currentData.summary.active = getActive(currentData);
          totalOutcome = currentData.summary.discharged + currentData.summary.deaths;
          const previousData = res.data[dataLen - 2];
          previousData.summary.active = getActive(previousData);
          diffData = getDiff(currentData, previousData);
          tableData = getTableData(currentData.regional);
          yesterdayTableData = getTableData(previousData.regional);
          updatedDate = res.lastOriginUpdate;
        }
        loading = false;
      });
  }

  onMount(() => {
    getData();
  });
</script>

<DashboardTitle {updatedDate} />
{#if currentData}
  <div class="main-data-container">
    <Box
      label="Total Confirmed"
      count="{currentData.summary.total}"
      type="confirmed"
      diff="{diffData.confirmed}"
      day="{currentData.day}"
    />
    <Box
      label="Total Active"
      count="{currentData.summary.active}"
      type="active"
      diff="{diffData.active}"
      day="{currentData.day}"
    />
    <Box
      label="Total Recoverd"
      count="{currentData.summary.discharged}"
      type="recovered"
      diff="{diffData.recovered}"
      day="{currentData.day}"
    />
    <Box
      label="Total Deaths"
      count="{currentData.summary.deaths}"
      type="deaths"
      diff="{diffData.deaths}"
      day="{currentData.day}"
    />
  </div>
  <div class="charts">
    <RcvrdVsDeathChart rawData="{totalData}" />
    <DailyChart rawData="{totalData}" />
  </div>
  <div class="cards-container mt-container">
    <div class="card">
      <div class="card-title">Outcome Analysis</div>
      <div class="card-body">
        <small>Cases which have outcome / closed</small>
        <div class="card-body-title">{totalOutcome}</div>
        <div class="card-content">
          <div>
            <small>Recovered / Discharged</small>
            <div class="card-item recovered">
              {currentData.summary.discharged} (
              <strong>{calculatePercentage(currentData.summary.discharged, totalOutcome)}%</strong>
              )
            </div>
          </div>
          <div>
            <small>Deaths / Deceased</small>
            <div class="card-item deaths">
              {currentData.summary.deaths} (
              <strong>{calculatePercentage(currentData.summary.deaths, totalOutcome)}%</strong>
              )
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="table-container mt-container">
    <Table {tableData} {yesterdayTableData} day="{currentData.day}" />
  </div>
{:else if loading}
  <PageLoader />
{/if}
