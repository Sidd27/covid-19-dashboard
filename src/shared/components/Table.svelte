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

  tableData = tableData.map(ele => {
    const prevStateData = prevTableDataMap[ele.state];
    if (prevStateData) {
      ele.diffConfirmed = ele.confirmed - prevStateData.confirmed;
      ele.diffActive = ele.active - prevStateData.active;
      ele.diffRecovered = ele.recovered - prevStateData.recovered;
      ele.diffDeaths = ele.deaths - prevStateData.deaths;
    }
    return ele;
  });

  let sameDate;

  function checkSameDate() {
    const date = new Date();
    const dateKey = `${date.getFullYear()}-${('0' + (date.getMonth() + 1)).slice(-2)}-${(
      '0' + date.getDate()
    ).slice(-2)}`;
    sameDate = day === dateKey;
  }

  checkSameDate();
</script>

<TableSort items="{tableData}">
  <tr slot="thead">
    <th data-sort="state">State / UT</th>
    <th data-sort="confirmed" data-sort-initial="descending">Confirmed</th>
    <th data-sort="diffConfirmed">New Confirmed</th>
    <th data-sort="active">Active</th>
    <th data-sort="diffActive">Changed Active</th>
    <th data-sort="recovered">Recovered</th>
    <th data-sort="diffRecovered">New Recovered</th>
    <th data-sort="deaths">Deaths</th>
    <th data-sort="diffDeaths">New Deaths</th>
  </tr>
  <tr slot="tbody" let:item="{data}">
    <td class="state">{data.state}</td>
    <td>{data.confirmed}</td>
    <td class="confirmed">{sameDate && data.diffConfirmed ? data.diffConfirmed : 0}</td>
    <td>{data.active}</td>
    <td class="hospitalized">{sameDate && data.diffActive ? data.diffActive : 0}</td>
    <td>{data.recovered}</td>
    <td class="recovered">{sameDate && data.diffRecovered ? data.diffRecovered : 0}</td>
    <td>{data.deaths}</td>
    <td class="deaths">{sameDate && data.diffDeaths ? data.diffDeaths : 0}</td>
  </tr>
</TableSort>
