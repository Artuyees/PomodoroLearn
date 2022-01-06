<script>
	import { tweened } from 'svelte/motion';

	let start = false;
	let currentTurn = 0;
	let turns = 2;
	let turnLength = 1;
	let interval;
	let timer = tweened(originalTime);
	const onInterval = (callback, milliseconds) => {
		interval = setInterval(callback, milliseconds);
	};
	const stopInterval = () => clearInterval(interval);
	const setTimer = () => ($timer = originalTime);
	const startCount = () => {
		let originalTime = turnLength * 5;
		start = !start;
		currentTurn++;
		console.log(turns, turnLength, turns, originalTime);
		onInterval(() => {
			$timer--;
			if ($timer == 0) {
				currentTurn++;
				setTimer();
			}
			if (currentTurn > turns) {
				stopInterval();
				currentTurn = 0;
				setTimer();
				start = !start;
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
