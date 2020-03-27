<style>
  nav {
    background-color: #fff;
    border-bottom: 1px solid rgba(0, 0, 0, 0.0625);
    height: 65px;
    display: flex;
    align-items: center;
    margin-bottom: 0;
    padding: 0;
    position: fixed;
    width: 100%;
    z-index: 10;
    padding: 0 20px;
  }

  ul {
    margin: 0;
    padding: 0;
  }
  /* clearfix */
  ul::after {
    content: '';
    display: block;
    clear: both;
  }
  li {
    display: block;
    float: left;
    width: 100px;
    position: relative;
    text-align: center;
  }

  li.active {
    border-bottom: 2px solid rgb(151, 45, 21);
  }

  a {
    text-decoration: none;
    padding: 20px 10px;
    display: inline-block;
  }

  img {
    height: 40px;
  }

  .hamburger {
    padding: 2px 10px 2px 0;
    height: 32px;
  }

  .logo {
    margin-right: 20px;
  }

  .logo-small {
    display: block;
  }
  .helpline {
    padding: 5px 10px;
    color: rgb(151, 45, 21);
    font-weight: 600;
    border-radius: 3px;
    border: 1px solid rgb(151, 45, 21);
    margin-left: auto;
    -webkit-transition: all 0.25s ease;
    -o-transition: all 0.25s ease;
    transition: all 0.25s ease;
  }
  .helpline:hover {
    background-color: rgb(151, 45, 21);
    color: #fff;
  }
</style>

<script>
  import HamburgeMenu from './HamburgerMenu.svelte';
  export let segment;

  let showMenu = false;
</script>

<nav class="hide-mobile">
  <img class="logo" src="logo.svg" alt="COVID-19-India" />
  <ul>
    <li class:active="{segment === undefined}">
      <a href=".">Offcial</a>
    </li>
    <li class:active="{segment === 'unofficial'}">
      <a href="unofficial">Unofficial</a>
    </li>
  </ul>
  <a class="helpline" href="tel:+911123978046">Call Helpline</a>
</nav>
<nav class="show-mobile">
  <img class="hamburger" src="hamburger.svg" alt="Menu" on:click="{() => (showMenu = true)}" />
  <img class="logo-small" src="logo-small.svg" alt="Coronavirus" />
  <HamburgeMenu bind:show="{showMenu}" {segment} />
  <a class="helpline" href="tel:+911123978046">Call Helpline</a>
</nav>
