<script>
	import "./styles.css";
	import { browser } from '$app/environment';
 
  if (browser) {
	import('bootstrap')
  }
</script>

<div class="app">
	<nav class="navbar navbar-expand-lg navbar-light bg-light">
		<div class="container-fluid">
			<a class="navbar-brand" href="/">Shopware Config Generator</a>
		</div>
	</nav>

	<main class="container">
		<slot />
	</main>
</div>

<style>
</style>
