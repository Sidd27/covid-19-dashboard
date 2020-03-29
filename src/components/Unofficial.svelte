<script>
  // 3rd Party Imports
  import { onMount } from 'svelte';
  import { TableSort } from 'svelte-tablesort';
  // Shared Resources
  import Box from '../shared/components/Box.svelte';
  import PageLoader from '../shared/components/PageLoader.svelte';
  import DashboardTitle from '../shared/components/DashboardTitle.svelte';
  import Table from '../shared/components/Table.svelte';
  // Chart Imports
  import AgeChart from './Charts/AgeChart.svelte';
  import GenderChart from './Charts/GenderChart.svelte';

  let currentData;
  let prevTableDataMap;
  let diffData;
  let rawPaitentData;
  let previousData;
  let loading = false;
  let updatedDate;

  function getDiff(current, prev) {
    return {
      confirmed: current.total.confirmed - prev.total.confirmed,
      recovered: current.total.recovered - prev.total.recovered,
      deaths: current.total.deaths - prev.total.deaths,
      active: current.total.active - prev.total.active
    };
  }

  async function getData() {
    loading = true;
    await fetch(`https://api.rootnet.in/covid19-in/unofficial/covid19india.org/statewise/history`)
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          const historyLen = res.data.history.length;
          currentData = res.data.history[historyLen - 1];

          previousData = res.data.history[historyLen - 2];
          diffData = getDiff(currentData, previousData);

          updatedDate = res.data.lastRefreshed;
        }
        loading = false;
      });
  }

  async function getRawPatientData() {
    await fetch(`https://api.rootnet.in/covid19-in/unofficial/covid19india.org`)
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          rawPaitentData = res.data.rawPatientData;
        }
      });
  }

  onMount(() => {
    getData();
    getRawPatientData();
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
  <div class="charts">
    {#if rawPaitentData}
      <AgeChart rawData="{rawPaitentData}" />
      <GenderChart rawData="{rawPaitentData}" />
    {/if}
  </div>
  <div class="table-container">
    <Table
      tableData="{currentData.statewise}"
      yesterdayTableData="{previousData.statewise}"
      day="{currentData.day}"
    />
  </div>
  <p class="table-legends">
    <small>&Delta; = Change in data from Previous day</small>
  </p>
  <p class="table-legends">
    <small>** = Based on partial data</small>
  </p>
  <p class="table-legends">
    <small>All coulums are sortable</small>
  </p>
{:else if loading}
  <PageLoader />
{/if}
