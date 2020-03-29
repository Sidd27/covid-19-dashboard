<script>
  // 3rd Party Imports
  import { onMount } from 'svelte';
  import { TableSort } from 'svelte-tablesort';
  // Shared Resources
  import Box from '../shared/components/Box.svelte';
  import PageLoader from '../shared/components/PageLoader.svelte';
  import DashboardTitle from '../shared/components/DashboardTitle.svelte';
  // Chart Imports
  import AgeChart from './Charts/AgeChart.svelte';
  import GenderChart from './Charts/GenderChart.svelte';

  let currentData;
  let prevTableDataMap;
  let diffData;
  let rawPaitentData;
  let isMobile = false;
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

          const previousData = res.data.history[historyLen - 2];
          diffData = getDiff(currentData, previousData);

          prevTableDataMap = previousData.statewise.reduce((initial, current) => {
            initial[current.state] = current;
            return initial;
          }, {});
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
    isMobile = window.innerWidth < 560;
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
    />
    <Box
      label="Total Active"
      count="{currentData.total.active}"
      type="active"
      diff="{diffData.active}"
    />
    <Box
      label="Total Recoverd"
      count="{currentData.total.recovered}"
      type="recovered"
      diff="{diffData.recovered}"
    />
    <Box
      label="Total Deaths"
      count="{currentData.total.deaths}"
      type="deaths"
      diff="{diffData.deaths}"
    />
  </div>
  <div class="charts">
    {#if rawPaitentData}
      <AgeChart rawData="{rawPaitentData}" />
      <GenderChart rawData="{rawPaitentData}" />
    {/if}
  </div>
  <div class="table-container">
    <TableSort items="{currentData.statewise}">
      <tr slot="thead">
        {#if isMobile}
          <th data-sort="state">State / UT</th>
          <th data-sort="confirmed" data-sort-initial="descending">CNFMRD</th>
          <th data-sort="active">ACTV</th>
          <th data-sort="recovered">RCVRD</th>
          <th data-sort="deaths">DCSD</th>
        {:else}
          <th data-sort="state">State / UT</th>
          <th data-sort="confirmed" data-sort-initial="descending">Confirmed</th>
          <th data-sort="active">Active</th>
          <th data-sort="recovered">Recovered</th>
          <th data-sort="deaths">Dealths</th>
        {/if}
      </tr>
      <tr slot="tbody" let:item="{data}">
        <td class="state">{data.state}</td>
        <td>
          {data.confirmed}
          <br />
          {#if data.confirmed - prevTableDataMap[data.state].confirmed !== 0}
            <small class="confirmed">
              {data.confirmed - prevTableDataMap[data.state].confirmed} &Delta;
            </small>
          {/if}
        </td>
        <td>
          {data.active}
          <br />
          {#if data.active - prevTableDataMap[data.state].active !== 0}
            <small class="hospitalized">
              {data.active - prevTableDataMap[data.state].active} &Delta;
            </small>
          {/if}
        </td>
        <td>
          {data.recovered}
          <br />
          {#if data.recovered - prevTableDataMap[data.state].recovered !== 0}
            <small class="recovered">
              {data.recovered - prevTableDataMap[data.state].recovered} &Delta;
            </small>
          {/if}
        </td>
        <td>
          {data.deaths}
          <br />
          {#if data.deaths - prevTableDataMap[data.state].deaths !== 0}
            <small class="deaths">
              {data.deaths - prevTableDataMap[data.state].deaths} &Delta;
            </small>
          {/if}
        </td>
      </tr>
    </TableSort>
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
