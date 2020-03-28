<style>
  .box-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .box-title {
    margin-bottom: 10px;
    text-align: left;
  }
  .box-data {
    font-size: 36px;
  }
  .box-badge {
    padding: 12px 26px;
    border-radius: 10px;
    font-weight: 600;
    height: 40px;
    line-height: 1;
  }
  .box {
    padding: 20px;
    text-align: center;
    border: 1px solid rgba(0, 0, 0, 0.0625);
    width: 22%;
    background-color: #fff;
  }
  .deaths .box-badge {
    background-color: #ffebee;
  }
  .confirmed .box-badge {
    background-color: #e3f2fd;
  }
  .recovered .box-badge {
    background-color: #e8f5e9;
  }
  .active {
    color: #9c27b0;
  }
  .active .box-badge {
    background-color: #f3e5f5;
  }
  @media (max-width: 992px) {
    .box {
      width: 48%;
    }
  }
  @media (max-width: 560px) {
    .box {
      width: 98%;
    }
  }
</style>

<script>
  export let count = 0;
  export let type;
  export let label;
  export let diff;
</script>

<div class="box">
  <div class="box-title">{label}</div>
  <div class="box-content {type}">
    <div class="box-data">{count}</div>
    <div class="box-badge">+{diff}</div>
  </div>
</div>
