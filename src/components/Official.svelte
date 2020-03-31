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
  import TotalChart from './Charts/TotalCaseChart.svelte';
  import LocVsForiegnChart from './Charts/LocalVsForeignChart.svelte';
  import RcvrdVsDeathChart from './Charts/RvrdVsDeadChart.svelte';
  import DailyChart from './Charts/DailyChart.svelte';

  let totalData;
  let currentData;
  let diffData;
  let tableData;
  let yesterdayTableData;
  let loading = false;
  let updatedDate;

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
          const previousData = res.data[dataLen - 2];
          previousData.summary.active = getActive(previousData);
          diffData = getDiff(currentData, previousData);
          tableData = getTableData(currentData.regional);
          yesterdayTableData = getTableData(previousData.regional);
          updatedDate = res.lastRefreshed;
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
    <TotalChart rawData="{totalData}" />
    <LocVsForiegnChart rawData="{totalData}" />
    <RcvrdVsDeathChart rawData="{totalData}" />
    <DailyChart rawData="{totalData}" />
  </div>
  <div class="table-container mt-container">
    <Table {tableData} {yesterdayTableData} day="{currentData.day}" />
  </div>
{:else if loading}
  <PageLoader />
{/if}
