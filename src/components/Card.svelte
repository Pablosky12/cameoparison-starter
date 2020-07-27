<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();
  export let showPrice;
  export let winner;
  export let celeb;
</script>

<style>
  .card-outer {
    width: 100%;
    height: 100%;
    position: relative;
  }

  .card-inner {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-size: cover;
    background: 50% 0 no-repeat;
    overflow: hidden;
    padding: 0;
    text-align: left;
  }

  .details {
    position: absolute;
    width: 100%;
    bottom: 0;
    padding: 1em 0.5em 0.5em 0.5em;
    background: linear-gradient(
      to bottom,
      transparent,
      rgba(0, 0, 0, 0.6) 35%,
      rgba(0, 0, 0, 0.9)
    );
    color: white;
  }

  .type {
    margin: 0;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  h2 {
    margin: 0;
    margin-bottom: 0.2em;
  }

  @media (min-width: 640px) {
    .card-outer {
      height: 0;
      padding: 0 0 100% 0;
    }
  }
  .price {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background-color: #000a;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 4em;
    font-weight: 700;
    color: white;
  }

  .price.large {
    font-size: 6em;
  }
</style>

<div class="card-outer">
  <button
    class="card-inner"
    style="background-image: url({celeb.image})"
    on:click={() => dispatch('select')}>
    <div class="details">
      <h2>
        <a target="_blank" href="https://cameo.com/{celeb.id}">{celeb.name}</a>
      </h2>
      <p class="type">{celeb.type}</p>
    </div>
    {#if showPrice}
      <div class="price" class:large={winner}>
        <span>${celeb.price}</span> 
      </div>
    {/if}
  </button>
</div>
