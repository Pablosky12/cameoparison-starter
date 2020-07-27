<script>
  import { createEventDispatcher } from "svelte";
  import Card from "../components/Card.svelte";
  import { fly, crossfade } from "svelte/transition";
  import * as eases from "svelte/easing";
  import { sleep, pick_random, loadImage } from "../utils";

  export let selection;
  let i = 0;
  let lastResult;
  let done = false;
  let ready = true;

  const dispatch = createEventDispatcher();

  const [send, receive] = crossfade({
    easing: eases.cubicOut,
    duration: 300,
  });

  $: score = results.filter((x) => x === "right").length;

  const pickMessage = (p) => {
    if (p < 0.5) return pick_random(["Ouch", "U suck"]);
    if (p <= 0.8)
      return pick_random(["you can do better", "keep trying, moron"]);
    return "Meh";
  };

  const load_details = async (celeb) => {
    const res = await fetch(
      `https://cameo-explorer.netlify.app/celebs/${celeb.id}.json`
    );
    const details = await res.json();
    await loadImage(details.image);
    return details;
  };

  const promises = selection.map((round) =>
    Promise.all([load_details(round.a), load_details(round.b)])
  );

  const results = Array(selection.length);

  const submit = async (a, b, sign) => {
    lastResult = Math.sign(a.price - b.price) === sign ? "right" : "wrong";

    await sleep(1500);
    results[i] = lastResult;
    lastResult = null;

    await sleep(300);
    if (i < selection.length - 1) {
      i += 1;
    } else {
      done = true;
    }
  };
</script>

<style>
  .game-container {
    flex: 1;
  }
  .error {
    color: red;
  }
  .game {
    display: grid;
    grid-template-rows: 1fr 2em 1fr;
    grid-gap: 0.5em;
    width: 100%;
    height: 100%;
    max-width: min(100%, 40vh);
    margin: 0 auto;
  }
  .game > div {
    display: flex;
    align-items: center;
  }

  .giant-result {
    position: fixed;
    width: 50vmin;
    height: 50vmin;
    left: calc(50vw - 25vmin);
    top: calc(50vh - 25vmin);
    opacity: 0.5;
  }

  .result {
    background: rgba(255, 255, 255, 0.1);
    border-radius: 50%;
    padding: 0 0 100% 0;
    transition: background 0.2s;
    transition-delay: 0.2s;
  }

  .result img {
    position: relative;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
  }

  .results {
    display: grid;
    grid-gap: 0.2em;
    width: 100%;
    max-width: 320px;
    margin: 1em auto 0 auto;
  }
  @media (min-width: 640px) {
    .game {
      max-width: 100%;
      grid-template-rows: none;
      grid-template-columns: 1fr 8em 1fr;

      max-height: calc(100vh - 6em);
    }

    .same {
      height: 8em;
    }
  }

  .done {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .done strong {
    font-size: 6em;
    font-weight: 700;
  }
</style>

<header>
  <p>
    Tap on the more monetisable celebrity's face, or tap same price if you think
    society values them equally
  </p>
</header>

<div class="game-container">
  {#if done}
    <div class="done">
      <strong>{score}/{results.length}</strong>
      <p>{pickMessage(score / results.length)}</p>
      <button on:click={() => dispatch('restart')}>Restart</button>
    </div>
  {:else if ready}
    {#await promises[i] then [a, b]}
      <div
        class="game"
        in:fly={{ duration: 200, y: 20 }}
        out:fly={{ duration: 200, y: -20 }}
        on:outrostart={() => {
          ready = false;
        }}
        on:outroend={() => {
          ready = true;
        }}>
        <div class="card-container">
          <Card
            celeb={a}
            on:select={() => submit(a, b, 1)}
            showPrice={lastResult}
            winner={a.price > b.price} />
        </div>
        <div>
          <button class="same" on:click={() => submit(a, b, 0)}>
            Same Price
          </button>
        </div>
        <div class="card-container">
          <Card
            celeb={b}
            on:select={() => submit(a, b, -1)}
            showPrice={lastResult}
            winner={a.price < b.price} />
        </div>
      </div>
    {:catch}
      <p class="error">Error</p>
    {/await}
  {/if}

</div>
{#if lastResult}
  <img
    class="giant-result"
    alt="{lastResult} answer"
    in:fly={{ x: 100, duration: 200 }}
    out:send={{ key: i }}
    src="/icons/{lastResult}.svg" />
{/if}
<div
  class="results"
  style="grid-template-columns: repeat({results.length}, 1fr)">
  {#each results as result, i}
    <span class="result">
      {#if result}
        <img
          in:receive={{ key: i }}
          alt="{result} answer"
          src="/icons/{result}.svg" />
      {/if}
    </span>
  {/each}
</div>
