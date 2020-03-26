<style>
  /* The side navigation menu */
  .side-menu {
    height: 100vh; /* Full-height */
    width: 250px;
    position: fixed; /* Stay in place */
    z-index: 1; /* Stay on top */
    top: 0; /* Stay at the top */
    left: 0; /* Stay at the left most */
    background-color: #fafafa;
    border-right: 1px solid rgba(0, 0, 0, 0.0625);
    overflow-x: hidden; /* Disable horizontal scroll */
  }

  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.325);
  }

  .menu-header {
    padding: 10px;
    line-height: 1;
    background-color: #fff;
    position: relative;
    border-bottom: 1px solid rgba(0, 0, 0, 0.0625);
  }

  a {
    text-decoration: none;
  }

  .menu-item {
    display: block;
    border-top: 1px solid rgba(0, 0, 0, 0.0625);
    border-bottom: 1px solid rgba(0, 0, 0, 0.0625);
    padding: 16px 8px 16px 32px;
    background-color: #fff;
    margin-top: 5px;
    font-size: 16px;
    font-weight: 600;
  }

  .menu-item.active {
    border-left: 5px solid rgb(151, 45, 21);
    padding-left: 36px;
  }

  .closebtn {
    position: absolute;
    top: 5px;
    right: 12px;
    padding: 5px;
    font-size: 24px;
    margin-left: 50px;
  }

  .logo {
    height: 30px;
    vertical-align: middle;
  }
</style>

<script>
  import { fly, fade } from 'svelte/transition';
  export let segment;
  export let show = false;

  function closeNav() {
    show = false;
  }
</script>

{#if show}
  <div class="overlay" on:click="{closeNav}" transition:fade></div>
  <aside class="side-menu" transition:fly="{{ duration: 400, x: -250, opacity: 1 }}">
    <div class="menu-header">
      <img class="logo" src="logo.svg" alt="COVID-19-India" />
      <a href="javascript:void(0)" class="closebtn" on:click="{closeNav}">&times;</a>
    </div>
    <a class="menu-item" class:active="{segment === undefined}" href="." on:click="{closeNav}">
      Offcial
    </a>
    <a
      class="menu-item"
      class:active="{segment === 'unofficial'}"
      href="unofficial"
      on:click="{closeNav}"
    >
      Unofficial
    </a>
  </aside>
{/if}
