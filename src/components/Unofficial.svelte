<style>
  .main-data-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
  }
  table,
  td,
  th {
    font-family: "Noto Sans", sans-serif;
    border: 1px solid #e9ecef;
    border-collapse: collapse;
    padding: 0.75rem;
    background-color: #ffffff;
  }

  /* tr:nth-child(even) td {
    background-color: #f2f2f2;
  } */

  tr:hover td {
    background-color: #f2f2f2;
  }

  table {
    margin-top: 10px;
    width: 71%;
    margin-left: 40px;
  }

  th {
    text-align: left;
  }

  .state {
    font-weight: 600;
    color: #72777a;
  }
</style>

<script>
  import { onMount } from "svelte";
  import Box from "../shared/components/Box.svelte";

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
  <table>
    <tr>
      <th>State</th>
      <th>Total Cases</th>
      <th>Total Active Cases</th>
      <th>Total Recovered</th>
      <th>Total Dealth</th>
    </tr>
    {#each currentData.statewise as data}
      <tr>
        <td class="state">{data.state}</td>
        <td>{data.confirmed}</td>
        <td>{data.active}</td>
        <td>{data.recovered}</td>
        <td>{data.deaths}</td>
      </tr>
    {/each}
  </table>
{/if}
