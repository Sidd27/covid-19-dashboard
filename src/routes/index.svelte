<script>
  import { onMount } from "svelte";

  let unoffcialData;
  onMount(async () => {
    await fetch(
      `https://api.rootnet.in/covid19-in/unofficial/covid19india.org/statewise`
    )
      .then(r => r.json())
      .then(res => {
        if (res.success) {
          unoffcialData = res.data;
        }
      });
  });
</script>

<style>
  .main-data-container {
    display: flex;
    justify-content: space-evenly;
  }
  .box {
    padding: 30px 20px;
    text-align: center;
    color: #ffffff;
    font-size: 36px;
    width: 22%;
    border-radius: 10px;
  }
  .total {
    background-color: #008bbb;
  }
  .active {
    background-color: #dc6800;
  }
  .recovered {
    background-color: #6ea12f;
  }
  .death {
    background-color: #e51f3b;
  }
</style>

<svelte:head>
  <title>Covid19 Status</title>
</svelte:head>

<section>
  {#if unoffcialData}
    <div class="main-data-container">
      <div class="box total">{unoffcialData.total.confirmed}</div>
      <div class="box active">{unoffcialData.total.active}</div>
      <div class="box recovered">{unoffcialData.total.recovered}</div>
      <div class="box death">{unoffcialData.total.deaths}</div>
    </div>

    <table>
      <thead>
        <tr>
          <th>State</th>
          <th>Confirmed</th>
          <th>Active</th>
          <th>Recovered</th>
          <th>Deaths</th>
        </tr>
      </thead>
      <tbody>
        {#each unoffcialData.statewise as data}
          <tr>
            <td>{data.state}</td>
            <td>{data.confirmed}</td>
            <td>{data.active}</td>
            <td>{data.recovered}</td>
            <td>{data.deaths}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
</section>
