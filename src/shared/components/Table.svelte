<script>
  import { onMount } from 'svelte';
  import { TableSort } from 'svelte-tablesort';

  export let tableData;
  export let yesterdayTableData;
  export let day;

  let prevTableDataMap = yesterdayTableData.reduce((inital, current) => {
    inital[current.state] = current;
    return inital;
  }, {});

  let isMobile = false;

  let sameDate;

  function checkSameDate() {
    const date = new Date();
    const dateKey = `${date.getFullYear()}-${('0' + (date.getMonth() + 1)).slice(-2)}-${(
      '0' + date.getDate()
    ).slice(-2)}`;
    sameDate = day === dateKey;
  }

  checkSameDate();

  onMount(() => {
    isMobile = window.innerWidth < 560;
  });
</script>

<TableSort items="{tableData}">
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
      {#if data.confirmed - prevTableDataMap[data.state].confirmed !== 0 && sameDate}
        <small class="confirmed">
          {data.confirmed - prevTableDataMap[data.state].confirmed} &Delta;
        </small>
      {/if}

    </td>
    <td>
      {data.active}
      <br />
      {#if data.active - prevTableDataMap[data.state].active !== 0 && sameDate}
        <small class="hospitalized">
          {data.active - prevTableDataMap[data.state].active} &Delta;
        </small>
      {/if}
    </td>
    <td>
      {data.recovered}
      <br />
      {#if data.recovered - prevTableDataMap[data.state].recovered !== 0 && sameDate}
        <small class="recovered">
          {data.recovered - prevTableDataMap[data.state].recovered} &Delta;
        </small>
      {/if}
    </td>
    <td>
      {data.deaths}
      <br />
      {#if data.deaths - prevTableDataMap[data.state].deaths !== 0 && sameDate}
        <small class="deaths">{data.deaths - prevTableDataMap[data.state].deaths} &Delta;</small>
      {/if}
    </td>
  </tr>
</TableSort>
