<script lang="ts">
	import AnimatedBG from '$lib/components/AnimatedBG.svelte';

	import { fade } from 'svelte/transition';
	import { page } from '$app/state';

	import '$lib/css/global.scss';

	const segment = page.url.pathname;
</script>

<div class="layout" in:fade={{ delay: 1000, duration: 1000 }} out:fade={{ duration: 1000 }}>
	{#if segment === '/'}
		<AnimatedBG />
	{/if}
	<main>
		<slot />
	</main>

	<footer>
		<span>Made with <a href="https://kit.svelte.dev/">SvelteKit</a> - Hosted on Github Pages</span>
	</footer>
</div>

<style>
	.layout {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
		width: 100vw;
		max-width: 100%;
	}

	main {
		flex: 1;
		position: relative;
		margin: 0 auto;
		max-width: 1200px;
		padding: 1rem 2rem;
		box-sizing: border-box;
		width: 100%;
		display: flex;
		flex-direction: column;
	}

	footer {
		color: rgb(68, 68, 68);
		font-size: 1rem;
		margin: 1rem auto;
		max-width: 1400px;
		padding: 1rem 2rem;
		text-align: center;
		width: 100%;
		z-index: 1;
	}
</style>
