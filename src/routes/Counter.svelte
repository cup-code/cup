<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { Spring } from 'svelte/motion';
	import { setContext } from 'svelte';
	import Child from './child.svelte';

	const count = new Spring(0);
	// const offset = $derived(() => modulo(count.current, 1));
	// function modulo(n: number, m: number) {
	// 	// handle negative numbers
	// 	return ((n % m) + m) % m;
	// }

	let a = 0;
	let b = 2;
	function add(a: number, b: number) {
		return a + b;
	}

	let total = add(a, b);
	a = 1;
	b = 3;

	const offsets = $state('inspect');
	const counter = $state({ count: 0 });

	$inspect(counter).with((type, counter) => {
		if (type === 'update') {
			console.log(counter);
		}
	});
	$inspect(offsets).with(console.log);
	function onclick() {
		counter.count += 1;
		// console.log(counter); //查看代理对象内容
		// console.log($state.snapshot(counter)); //直接产看对象内容
	}

	setContext('my-context', counter.count);

	// 当该对象属性的值发生变化都会运行
	$effect(() => {
		$inspect.trace();
		counter.count;
		setTimeout(() => {
			console.log(offsets);
		}, 100);
		console.log(111);
	});

	//只会运行一次
	$effect(() => {
		counter;

		console.log(2);
	});

	import { tick } from 'svelte';

	let div = $state<HTMLDivElement>();

	let messages = $state([
		{ id: 2, name: 'test' },
		{ id: 3, name: 'test2' },
		{ id: 4, name: 'test3' }
	]);

	// ...

	$effect(() => {
		if (!div) return; // not yet mounted

		// reference `messages` array length so that this code re-runs whenever it changes
		console.log(messages.length, 333);

		// autoscroll when new messages are added
		if (div.offsetHeight + div.scrollTop > div.scrollHeight - 20) {
			tick().then(() => {
				console.log(div, div?.scrollHeight);
				div?.scrollTo(0, div.scrollHeight);
			});
		}
	});

	let { children } = $props();

	let boxes = $state([
		{ width: 10, height: 10 },
		{ width: 20, height: 20 },
		{ width: 30, height: 30 }
	]);
	let checked = $state(false);
	import type { Action } from 'svelte/action';

	const myaction: Action<HTMLDivElement, { info: string }> = (node, datas) => {
		// the node has been mounted in the DOM

		console.log(node, datas);

		$effect(() => {
			// setup goes here

			return () => {
				// teardown goes here
			};
		});
	};

	let data = { info: 'kkkk' };
	import { fade, fly } from 'svelte/transition';

	let visible = $state(false);

	import { elasticOut } from 'svelte/easing';
	let show = $state(false);
	// function whoosh(
	// 	node: HTMLElement,
	// 	params: { delay?: number; duration?: number; easing?: (t: number) => number }
	// ) {
	// 	const existingTransform = getComputedStyle(node).transform.replace('none', '');

	// 	return {
	// 		delay: params.delay || 0,
	// 		duration: params.duration || 400,
	// 		easing: params.easing || elasticOut,
	// 		css: (t, u) => `transform: ${existingTransform} scale(${t})`
	// 	};
	// }
	import { cubicOut } from 'svelte/easing';
	function whizz(node: HTMLElement, { from, to }: { from: DOMRect; to: DOMRect }, params: any) {
		const dx = from.left - to.left;
		const dy = from.top - to.top;

		const d = Math.sqrt(dx * dx + dy * dy);

		return {
			delay: 0,
			duration: Math.sqrt(d) * 120,
			easing: cubicOut,
			css: (t: number, u: number) =>
				`transform: translate(${u * dx}px, ${u * dy}px) rotate(${t * 360}deg);`
		};
	}

	const list = $state([
		{ id: 1, name: 'test' },
		{ id: 2, name: 'test2' },
		{ id: 3, name: 'test3' }
	]);

	import { writable, derived, readable } from 'svelte/store';

	const counts = writable(0, (set, update) => {
		// set(4);
	});

	const time = readable(new Date(), (set) => {
		set(new Date());

		const interval = setInterval(() => {
			set(new Date());
		}, 1000);
		return () => clearInterval(interval);
	});

	console.log('time', time);

	const delayed = derived(counts, ($counts, set) => {
		console.log(2345, $counts);
		setTimeout(() => set($counts), 1000);
	});

	counts.set(4); // does nothing
	const unsubscribe = counts.subscribe((value) => {
		console.log(22422, value);
	}); // logs 'got a subscriber', then '1'
	const delayedSub = delayed.subscribe((value) => {
		console.log(1111, value);
	}); // logs 'got a subscriber', then '1'
	counts.update((n) => n + 1); // logs '2'
	unsubscribe(); // logs 'no more subscribers'
	delayedSub();

	import { onMount, onDestroy } from 'svelte';
	onMount(() => {
		console.log('onMount');
	});
	onDestroy(() => {
		console.log('onDestroy');
	});

	$effect.pre(() => {
		console.log(counter.count, 999);
		tick().then(() => {
			console.log('tick');
		});
	});
</script>

<div class="flex">
	<div class="w-1/2">
		<Child />
		<div style="--track-color:yellow;--title-size:16px">
			<button class="btn-block">样式继承</button>
			<text>R&B</text>
		</div>
		<div class="btn-block" style="background-color: var(--color-secondary)">同级元素</div>
		<Button variant="destructive">shadcn-button</Button>
		<div class="flex">
			{#each list as item, index (item.id)}
				<div class="flex" animate:whizz={item}>
					{index}：{item.name}
				</div>
			{/each}
		</div>
		<div class="my-3">
			<div>动画效果</div>
			<div class="flex">
				<button class="mr-2 rounded bg-amber-300 px-2 py-1" onclick={() => (visible = !visible)}>
					in && out
				</button>
				{#if visible}
					<div class="h-7 bg-blue-300 leading-7" in:fly={{ y: 200 }} out:fade>fades in and out</div>
				{/if}
			</div>
		</div>
		<div class="my-2" use:myaction={data}>
			....可以获取改元素节点信息<span class="text-2xl">use</span>
		</div>

		<form class="my-3">
			<span>可以设置默认值：</span>
			<input type="checkbox" bind:checked defaultChecked={true} />
			<input type="reset" value="Reset" />
		</form>

		<div>
			<div class="text-2xl font-medium text-blue-500">snippet && render</div>
			{@render children?.('lila')}
			{#if children}
				{@render children('Elena')}
			{:else}
				fallback content
			{/if}
		</div>

		<div bind:this={div} class="flex-col">
			<div>#each as</div>
			{#each messages as { id, ...rest }, i (id)}
				<p class="m-2 w-19 bg-blue-500 p-2 text-white">{i}：{Object.values(rest)}</p>
			{/each}

			<!-- {@debug} -->
			{#each boxes as box}
				{@const area = box.width * box.height}
				<p class="mx-5">{box.width} * {box.height} = {area}</p>
			{/each}
		</div>
	</div>

	<div class="w-1/2">
		<div class="flex">
			{#each { length: 2 }, rank}
				{#each { length: 2 }, file}
					<div class="mx-7 h-6 w-20 bg-blue-500 text-center">{rank}{file}</div>
				{/each}
			{/each}
		</div>
		<button class="my-3 bg-blue-500 p-2 text-white" {onclick}>Click me</button>

		<h1>Counter: {counter.count}</h1>
		<!-- <button onclick={() => dispatch('add')}>Counter: {counter.count}</button> -->

		<div class="counter">
			<button onclick={() => (count.target -= 1)} aria-label="Decrease the counter by one">
				<svg aria-hidden="true" viewBox="0 0 1 1">
					<path d="M0,0.5 L1,0.5" />
				</svg>
			</button>

			<div class="counter-viewport">
				<div class="counter-digits" style="transform: translate(0, {100}%)">
					<strong class="hidden" aria-hidden="true">{Math.floor(count.current + 1)}</strong>
					<strong>{Math.floor(count.current)}</strong>
				</div>
			</div>

			<button onclick={() => (count.target += 1)} aria-label="Increase the counter by one">
				<svg aria-hidden="true" viewBox="0 0 1 1">
					<path d="M0,0.5 L1,0.5 M0.5,0 L0.5,1" />
				</svg>
			</button>
		</div>
	</div>
</div>

<style>
	.btn-block {
		color: var(--track-color, '#ffffff');
		font-size: var(--font-size, '12px');
	}

	.counter {
		display: flex;
		border-top: 1px solid rgba(0, 0, 0, 0.1);
		border-bottom: 1px solid rgba(0, 0, 0, 0.1);
		margin: 1rem 0;
	}

	.counter button {
		width: 2em;
		padding: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 0;
		background-color: transparent;
		touch-action: manipulation;
		font-size: 2rem;
	}

	.counter button:hover {
		background-color: var(--color-bg-1);
	}

	svg {
		width: 25%;
		height: 25%;
	}

	path {
		vector-effect: non-scaling-stroke;
		stroke-width: 2px;
		stroke: #444;
	}

	.counter-viewport {
		width: 8em;
		height: 4em;
		overflow: hidden;
		text-align: center;
		position: relative;
	}

	.counter-viewport strong {
		position: absolute;
		display: flex;
		width: 100%;
		height: 100%;
		font-weight: 400;
		color: var(--color-theme-1);
		font-size: 4rem;
		align-items: center;
		justify-content: center;
	}

	.counter-digits {
		position: absolute;
		width: 100%;
		height: 100%;
	}

	.hidden {
		top: -100%;
		user-select: none;
	}
</style>
