<style>
  nav {
    background-color: #fff;
    border-bottom: 1px solid rgba(0, 0, 0, 0.0625);
    display: flex;
    align-items: center;
    height: 65px;
    margin-bottom: 0;
    padding: 0;
    position: fixed;
    -webkit-transition: all 0.2s ease;
    -o-transition: all 0.2s ease;
    transition: all 0.2s ease;
    width: 100%;
    z-index: 800;
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
    border-bottom: 2px solid rgb(255, 62, 0);
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
    padding: 2px 10px;
    height: 32px;
  }

  .logo {
    margin-right: 50px;
  }

  .logo-small {
    display: block;
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
</nav>

<nav class="show-mobile">
  <img class="hamburger" src="hamburger.svg" alt="Menu" on:click="{() => (showMenu = true)}" />
  <img class="logo-small" src="logo-small.svg" alt="Coronavirus" />
  <HamburgeMenu bind:show="{showMenu}" {segment} />
</nav>
