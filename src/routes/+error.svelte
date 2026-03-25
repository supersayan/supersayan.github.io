<script>
	import { page } from '$app/state';
</script>

<style>

	h1 {
		font-size: 2.8em;
		font-weight: 700;
		margin: 1em auto;
		text-indent: 0;
	}

	@media (min-width: 480px) {
		h1 {
			font-size: 4em;
		}
	}
</style>

<svelte:head>
	<title>{page.status}</title>
</svelte:head>

<h1>{page.status}</h1>
<h1>{page.error?.message}</h1>
