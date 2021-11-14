<script>
  import { onMount } from "svelte";

  import { Router, Link, Route } from "svelte-routing/src";
  // import Link from "svelte-routing/src/Link.svelte";
  // import Router from "svelte-routing/src/Router.svelte";
  // import Route from "svelte-routing/src/Route.svelte";
  import SpaLayout from "./SpaLayout.svelte";

  export let url = "";

  let disabled = true;
  onMount(() => {
    if (typeof window !== "undefined") {
      disabled = false;
    }
  });

  let counter = 0;
  const increment = () => {
    counter += 1;
  };
</script>

<Router {url}>
  <nav class="nav-spa stack horizontal">
    <Link to="/tests/spa/">SPA index</Link>
    <Link to="/tests/spa/one/">SPA page one</Link>
  </nav>
  <div>
    <Route path="/tests/spa/">
      <SpaLayout text="SPA: Page index" />
    </Route>
    <Route path="/tests/spa/one/">
      <SpaLayout text="SPA: Page one" />
    </Route>
  </div>
  <h2>A counter for good measure</h2>
  <button on:click={increment} {disabled}>
    This button has been clicked {counter} time{counter > 1 ? `s` : ""}
  </button>
</Router>

<style>
  :global(.nav-spa a) {
    padding: var(--gap, 1rem) 0;
  }
</style>
