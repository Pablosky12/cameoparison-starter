<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();
  const categories = [
    { slug: "actors", label: "actors" },
    { slug: "athletes", label: "athletes" },
    { slug: "comedians", label: "comedians" },
    { slug: "creators", label: "creators" },
    { slug: "models", label: "models" },
    { slug: "musicians", label: "musicians" },
    { slug: "reality-tv", label: "reality TV" },
  ];

  let selected = false;
  const select = (category) => {
    selected = true;
    dispatch("select", { category });
  };
</script>

<style>
  h1 {
    color: rgb(150, 56, 150);
    text-transform: uppercase;
    font-weight: 100;
    font-size: min(12vw, 4em);
  }
  p {
    max-width: 30em;
    margin: 1em auto;
  }

  .logo {
    display: inline-block;
    background: url("/icons/compare.svg");
    background-size: 100% 100%;
    width: 1em;
    transform: scale(1.4);
    text-indent: -100000px;
  }
</style>

<header>
  <h1>
    CameoP
    <span class="logo">a</span>
    rison
  </h1>
  <p>
    This project is made following the Rich Harris tutorial on svelte on
    FrontendMasters
  </p>
</header>

<div class="categories">
  {#each categories as category}
    <button disabled={selected} on:click={() => select(category)}>{category.label}</button>
  {/each}
</div>
