<script>
	import { onDestroy } from 'svelte';
	import { tweened } from 'svelte/motion';
	let interval;
	let start = false;
	let currentTurn = 0;
	let turns = 2;
	let turnLength = 1;
	let originalTime = turnLength * 10;
	let timer = tweened(originalTime);
	const onInterval = (callback, milliseconds) => {
		interval = setInterval(callback, milliseconds);

		onDestroy(() => {
			clearInterval(interval);
		});
	};
	const startCount = () => {
		start = !start;
		currentTurn++;
		$timer = $timer * turnLength;
		onInterval(() => {
			$timer--;
			console.log(start, $timer, currentTurn, turns, turnLength, originalTime);
			if ($timer == 0) {
				currentTurn++;
				$timer = originalTime;
			}
			if (currentTurn > turns) {
				clearInterval(interval);
				currentTurn = 0;
				$timer = originalTime;
				start = !start;
			}
			if ($timer < 0) {
				clearInterval(interval);
			}
		}, 1000);
	};
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: minname = minutes > 1 ? 'mins' : 'min';
	$: turnName = turns > 1 ? 'turns' : 'turn';
	$: currentTurnName = currentTurn > 1 ? 'turns' : 'turn';
</script>

{#if start}
	<p>
		remaining time: {#if minutes >= 1} {minutes} {minname} and {/if}
		{seconds} seconds of {currentTurn}
		{currentTurnName} out of {turns}
		{turnName}
	</p>
{:else}
	<h1>Welcome to Pomodoro app</h1>
	<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
	<button on:click={startCount}>Start timer</button>
	<input type="number" bind:value={turnLength} />
	<input type="number" bind:value={turns} />
{/if}
