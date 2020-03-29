<style>
  .title-container {
    display: flex;
    padding: 0 10px;
    justify-content: space-between;
  }
  h1 {
    margin-bottom: 4px;
  }
  .last-updated {
    font-weight: 600;
    color: #72777a;
    margin-bottom: 1em;
    text-align: right;
  }
</style>

<script>
  import TimeSince from '../utility/time-since.js';
  export let updatedDate;
  $: lastUpdated = updatedDate ? TimeSince(new Date(updatedDate)) : null;
</script>

<div class="title-container">
  <h1>Covid 19 Dashboard</h1>
  <div class="last-updated">
    <div>LAST UPDATED</div>
    {#if lastUpdated}{lastUpdated}{/if}
  </div>
</div>
