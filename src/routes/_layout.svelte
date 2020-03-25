<style>
  main {
    padding-top: 85px;
    position: relative;
    margin: 0 auto;
  }
</style>

<script>
  import Nav from "../components/Nav.svelte";
  export let segment;
</script>

<Nav {segment} />
<main>
  <slot />
</main>
