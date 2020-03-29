<script>
  // 3rd Party Imports
  import { onMount } from 'svelte';
  import Chart from 'chart.js';
  import { TableSort } from 'svelte-tablesort';
  // Shared Resources
  import Box from '../shared/components/Box.svelte';
  import PageLoader from '../shared/components/PageLoader.svelte';
  import DashboardTitle from '../shared/components/DashboardTitle.svelte';
  // Chart Imports
  import TotalChart from './Charts/TotalCaseChart.svelte';
  import LocVsForiegnChart from './Charts/LocalVsForeignChart.svelte';
  import RcvrdVsDeathChart from './Charts/RvrdVsDeadChart.svelte';
  import DailyChart from './Charts/DailyChart.svelte';

  let totalData;
  let currentData;
  let diffData;
  let tableData;
  let prevTableDataMap;
  let isMobile = false;
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
      ele.total = ele.confirmedCasesIndian + ele.confirmedCasesForeign;
      ele.active = ele.total - (ele.discharged + ele.deaths);
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

          const prevTableData = getTableData(previousData.regional);
          prevTableDataMap = prevTableData.reduce((inital, current) => {
            inital[current.loc] = current;
            return inital;
          }, {});
          updatedDate = res.lastOriginUpdate;
        }
        loading = false;
      });
  }

  onMount(() => {
    getData();
    isMobile = window.innerWidth < 560;
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
  <div class="charts">
    <TotalChart rawData="{totalData}" />
    <LocVsForiegnChart rawData="{totalData}" />
    <RcvrdVsDeathChart rawData="{totalData}" />
    <DailyChart rawData="{totalData}" />
  </div>
  <div class="table-container">
    <TableSort items="{tableData}">
      <tr slot="thead">
        {#if isMobile}
          <th data-sort="loc">State / UT</th>
          <th data-sort="total" data-sort-initial="descending">CNFMRD</th>
          <th data-sort="active">ACTV</th>
          <th data-sort="discharged">RCVRD</th>
          <th data-sort="deaths">DCSD</th>
        {:else}
          <th data-sort="loc">State / UT</th>
          <th data-sort="total" data-sort-initial="descending">Confirmed</th>
          <th data-sort="active">Active</th>
          <th data-sort="discharged">Recovered</th>
          <th data-sort="deaths">Dealths</th>
        {/if}
      </tr>
      <tr slot="tbody" let:item="{data}">
        <td class="state">{data.loc}</td>
        <td>
          {data.total}
          <br />
          {#if data.total - prevTableDataMap[data.loc].total !== 0}
            <small class="confirmed">{data.total - prevTableDataMap[data.loc].total} &Delta;</small>
          {/if}

        </td>
        <td>
          {data.active}
          <br />
          {#if data.active - prevTableDataMap[data.loc].active !== 0}
            <small class="hospitalized">
              {data.active - prevTableDataMap[data.loc].active} &Delta;
            </small>
          {/if}
        </td>
        <td>
          {data.discharged}
          <br />
          {#if data.discharged - prevTableDataMap[data.loc].discharged !== 0}
            <small class="recovered">
              {data.discharged - prevTableDataMap[data.loc].discharged} &Delta;
            </small>
          {/if}
        </td>
        <td>
          {data.deaths}
          <br />
          {#if data.deaths - prevTableDataMap[data.loc].deaths !== 0}
            <small class="deaths">{data.deaths - prevTableDataMap[data.loc].deaths} &Delta;</small>
          {/if}
        </td>
      </tr>
    </TableSort>
  </div>
  <p class="table-legends">
    <small>&Delta; is change in Data</small>
  </p>
  <p class="table-legends">
    <small>All coulums are sortable</small>
  </p>
{:else if loading}
  <PageLoader />
{/if}
