<script>
  import Welcome from "./screens/Welcome.svelte";
  import Game from "./screens/Game.svelte";
  import { onMount } from "svelte";
  import { select } from "./select";

  let celebsPromise;
  let state = "welcome";
  let selection;

  const start = async (e) => {
    const { celebs, lookup } = await celebsPromise;

    selection = select(celebs, lookup, e.detail.category.slug);
    state = "playing";
  };

  onMount(() => {
    celebsPromise = loadCelebs();
  });
  const loadCelebs = async () => {
    const res = await fetch("https://cameo-explorer.netlify.app/celebs.json");
    const data = await res.json();

    const lookup = new Map();

    data.forEach((c) => {
      lookup.set(c.id, c);
    });

    const subset = new Set();
    data.forEach((c) => {
      if (c.reviews >= 25) {
        subset.add(c);
        c.similar.forEach((id) => {
          subset.add(lookup.get(id));
        });
      }
    });

    return {
      celebs: Array.from(subset),
      lookup,
    };
  };
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 800px;
    margin: 0 auto;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
</style>

<main>
  {#if state === 'welcome'}
    <Welcome on:select={start} />
  {:else if state === 'playing'}
    <Game {selection} />
  {/if}
</main>
