<script>
	import { onDestroy } from 'svelte';
	import { tweened } from 'svelte/motion';
	let interval;
	let isBreak = false;
	let breakTime = 5;
	let start = false;
	let currentTurn = 0;
	let finish = false;
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
		breakTime *= 60;
		onInterval(() => {
			$timer--;

			if (Math.floor($timer) == 0 && !isBreak) {
				isBreak = !isBreak;
				$timer = breakTime;
			}
			if (Math.floor($timer) == 0 && isBreak) {
				currentTurn++;
				$timer = originalTime;
				isBreak = !isBreak;
			}
			if (currentTurn > turns) {
				clearInterval(interval);
				currentTurn = 0;
				start = !start;
				$timer = originalTime;
				Finish();
			}
			if ($timer < 0) {
				clearInterval(interval);
			}
		}, 1000);
	};
	const Finish = () => {
		finish = true;
		setTimeout(() => {
			finish = false;
		}, 4000);
	};
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: minname = minutes > 1 ? 'mins' : 'min';
	$: turnName = turns > 1 ? 'turns' : 'turn';
	$: currentTurnName = currentTurn > 1 ? 'turns' : 'turn';
</script>

{#if start}
	{#if !isBreak}
		<p>
			remaining time: {#if minutes >= 1} {minutes} {minname} and {/if}
			{seconds} seconds of {currentTurn}
			{currentTurnName} out of {turns}
			{turnName}
		</p>
	{:else}
		<p>
			remaining time: {#if minutes >= 1} {minutes} {minname} and {/if}
			{seconds} seconds of break
		</p>
	{/if}

	<progress max="1" value={$timer / (isBreak ? breakTime : originalTime)} />
{:else if finish}
	<p>Congrats</p>
{:else}
	<h1>Welcome to Pomodoro app</h1>
	<button on:click={startCount}>Start timer</button>
	<input type="number" bind:value={turnLength} />
	<input type="number" bind:value={turns} />
	<input type="number" bind:value={breakTime} />
{/if}

<style>
	progress {
		display: block;
		width: 33%;
	}
</style>
