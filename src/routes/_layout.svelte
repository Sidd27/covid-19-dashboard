<style>
  main {
    padding-top: 85px;
    position: relative;
    margin: 0 auto;
    min-height: calc(100vh - 85px);
  }
</style>

<script>
  import Nav from '../components/Nav.svelte';
  import Footer from '../components/Footer.svelte';

  export let segment;
</script>

<svelte:head>
  <title>Covid19 India</title>
</svelte:head>

<Nav {segment} />
<main>
  <slot />
</main>
<Footer />
