<script>
	import Welcome from './screens/welcome.svelte'
  let state = "welcome";
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
  <Welcome/>
</main>
