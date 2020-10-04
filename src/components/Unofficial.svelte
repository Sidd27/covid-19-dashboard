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
  import { TableSort } from 'svelte-tablesort';
  // Shared Resources
  import Box from '../shared/components/Box.svelte';
  import PageLoader from '../shared/components/PageLoader.svelte';
  import DashboardTitle from '../shared/components/DashboardTitle.svelte';
  import Table from '../shared/components/Table.svelte';

  let currentData;
  let prevTableDataMap;
  let diffData;
  let previousData;
  let loading = false;
  let updatedDate;
  let totalOutcome;

  function getDiff(current, prev) {
    return {
      confirmed: current.total.confirmed - prev.total.confirmed,
      recovered: current.total.recovered - prev.total.recovered,
      deaths: current.total.deaths - prev.total.deaths,
      active: current.total.active - prev.total.active
    };
  }

  function calculatePercentage(num, total) {
    return ((num / total) * 100).toFixed(2);
  }

  async function getData() {
    loading = true;
    await fetch(`https://api.rootnet.in/covid19-in/unofficial/covid19india.org/statewise/history`)
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          const historyLen = res.data.history.length;
          currentData = res.data.history[historyLen - 1];
          totalOutcome = currentData.total.recovered + currentData.total.deaths;
          previousData = res.data.history[historyLen - 2];
          diffData = getDiff(currentData, previousData);

          updatedDate = res.data.lastRefreshed;
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
      count="{currentData.total.confirmed}"
      type="confirmed"
      diff="{diffData.confirmed}"
      day="{currentData.day}"
    />
    <Box
      label="Total Active"
      count="{currentData.total.active}"
      type="active"
      diff="{diffData.active}"
      day="{currentData.day}"
    />
    <Box
      label="Total Recoverd"
      count="{currentData.total.recovered}"
      type="recovered"
      diff="{diffData.recovered}"
      day="{currentData.day}"
    />
    <Box
      label="Total Deaths"
      count="{currentData.total.deaths}"
      type="deaths"
      diff="{diffData.deaths}"
      day="{currentData.day}"
    />
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
              {currentData.total.recovered} (
              <strong>{calculatePercentage(currentData.total.recovered, totalOutcome)}%</strong>
              )
            </div>
          </div>
          <div>
            <small>Deaths / Deceased</small>
            <div class="card-item deaths">
              {currentData.total.deaths} (
              <strong>{calculatePercentage(currentData.total.deaths, totalOutcome)}%</strong>
              )
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="table-container mt-container">
    <Table
      tableData="{currentData.statewise}"
      yesterdayTableData="{previousData.statewise}"
      day="{currentData.day}"
    />
  </div>
{:else if loading}
  <PageLoader />
{/if}
