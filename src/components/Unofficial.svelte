<style>
  .main-data-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
  }
</style>

<script>
  import { onMount } from "svelte";
  import Box from "../shared/components/Box.svelte";
  import { TableSort } from "svelte-tablesort";

  let currentData;
  let previousData;
  let diffData;

  function getDiff(current, prev) {
    return {
      confirmed: current.total.confirmed - prev.total.confirmed,
      recovered: current.total.recovered - prev.total.recovered,
      deaths: current.total.deaths - prev.total.deaths,
      active: current.total.active - prev.total.active
    };
  }

  async function getData() {
    await fetch(
      `https://api.rootnet.in/covid19-in/unofficial/covid19india.org/statewise/history`
    )
      .then(r => r.json())
      .then(res => {
        if (res.success && res.data) {
          currentData = res.data.history[1];
          previousData = res.data.history[0];
          diffData = getDiff(currentData, previousData);
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
  <TableSort items="{currentData.statewise}">
    <tr slot="thead">
      <th data-sort="state">State</th>
      <th data-sort="confirmed" data-sort-initial="descending">Total Cases</th>
      <th data-sort="active">Total Active Cases</th>
      <th data-sort="recovered">Total Recovered</th>
      <th data-sort="deaths">Total Dealths</th>
    </tr>
    <tr slot="tbody" let:item="{data}">
      <td class="state">{data.state}</td>
      <td>{data.confirmed}</td>
      <td>{data.active}</td>
      <td>{data.recovered}</td>
      <td>{data.deaths}</td>
    </tr>
  </TableSort>
{/if}
