<script>
  import Nav from "../components/Nav.svelte";
</script>

<style>
  main {
    padding-top: 85px;
    position: relative;
    margin: 0 auto;
  }
</style>

<Nav />
<main>
  <slot />
</main>
