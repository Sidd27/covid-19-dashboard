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
    content: "";
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

  .logo-small {
    display: none;
    margin-right: 50px;
  }
  @media (max-width: 560px) {
    .logo-small {
      display: block;
      margin: 0 20px 0 10px;
    }
    .logo {
      display: none;
    }
  }
</style>

<script>
  export let segment;
</script>

<nav>
  <img class="logo" src="logo.svg" alt="Coronavirus" />
  <img class="logo-small" src="logo-small.svg" alt="Coronavirus" />
  <ul>
    <li class:active="{segment === undefined}">
      <a href=".">Offcial</a>
    </li>
    <li class:active="{segment === 'unofficial'}">
      <a href="unofficial">Unofficial</a>
    </li>
  </ul>
</nav>
