<script>
  import Nav from "../components/Nav.svelte";
</script>

<style>
  main {
    padding-top: 85px;
    position: relative;
    max-width: 56em;
    margin: 0 auto;
  }
</style>

<Nav />
<main>
  <slot />
</main>
