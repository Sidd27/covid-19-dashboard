<style>
  .main-data-container {
    display: flex;
    justify-content: space-evenly;
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
{/if}
