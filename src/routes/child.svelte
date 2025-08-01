<script lang="ts">
	import { getContext } from 'svelte';

	const myContext = getContext('my-context');
	console.log('myContext', myContext);
</script>

<div>
	<h1>Child</h1>
	<p>{myContext}</p>
</div>
