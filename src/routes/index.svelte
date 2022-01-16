<script lang="ts">
  // import 'bulma/css/bulma.css'
  import Playground from '../components/Playground.svelte'
</script>

<main>
  <h1>Svelte Hint</h1>

  <p>
    Svelte utility library for Tooltips. Heavy lifting of positioning is done by
    <a href="https://floating-ui.com/">Floating UI</a>.
  </p>
  <p>
    If you are looking for installation and docs please see the
    <a href="https://github.com/cupcakearmy/svelte-hint">repository</a>.
  </p>

  <Playground />
</main>

<style>
  main {
    margin: auto;
    padding: 1rem;
    max-width: 40rem;
  }
</style>
