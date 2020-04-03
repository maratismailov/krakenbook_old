<script>
  import Nav from "../components/Nav.svelte";

  export let segment;
</script>

<style>
  main {
    position: relative;
    max-width: 56em;
    /* background-color: #1e1e1e;
    color: #9cdcfe; */
    padding: 2em;
    margin: 0 auto;
    box-sizing: border-box;
  }
  :global(body) {
    /* this will apply to <body> */
    background-color: #1e1e1e;
    color: #9cdcfe;
  }
</style>

<Nav {segment} />

<main>
  <slot />
</main>
