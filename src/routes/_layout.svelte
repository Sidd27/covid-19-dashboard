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

<svelte:head>
  <title>Covid19 India</title>
</svelte:head>

<Nav {segment} />
<main>
  <slot />
</main>
