<style>
  main {
    padding: 85px 3% 0;
    position: relative;
    margin: 0 auto;
    min-height: calc(100vh - 85px);
  }
  @media (max-width: 992px) {
    main {
      padding: 85px 2% 0;
    }
  }
  @media (max-width: 560px) {
    main {
      padding: 85px 1% 0;
    }
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
