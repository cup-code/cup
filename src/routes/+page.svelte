<script lang="ts">
	import Counter from './Counter.svelte';
	import welcome from '$lib/images/svelte-welcome.webp';
	import welcomeFallback from '$lib/images/svelte-welcome.png';
	import { mount, hydrate } from 'svelte';
	import { render } from 'svelte/server';

	let tag = $state('hr');

	// const app = mount(Counter);

	// const result = render(Counter, {
	// 	props: { children }
	// });

	// console.log('result.body', result.body);
</script>

{#snippet children(name: string)}
	<div class="text-left">
		<div class="text-2xl font-medium text-red-200">{name}</div>
	</div>
{/snippet}

<svelte:element this={tag}> This text cannot appear inside an hr element </svelte:element>
<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<!-- <h1> -->
	<!-- <span class="welcome">
			<picture>
				<source srcset={welcome} type="image/webp" />
				<img src={welcomeFallback} alt="Welcome" />
			</picture>
		</span> -->

	<!-- to your new<br />SvelteKit app -->
	<!-- </h1> -->

	<!-- <h2>
		try editing <strong>src/routes/+page.svelte</strong>
	</h2> -->

	<Counter {children} />
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		display: block;
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
