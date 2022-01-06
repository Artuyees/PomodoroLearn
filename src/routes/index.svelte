<script>
	import { onDestroy } from 'svelte';
	import { tweened } from 'svelte/motion';
	let interval;
	let isBreak = false;
	let breakTime = 5;
	let isStart = false;
	let currentTurn = 0;
	let isFinish = false;
	let turns = 2;
	let turnLength = 1;
	let originalTime = turnLength * 60;
	let timer = tweened(originalTime);
	const onInterval = (callback, ms) => {
		interval = setInterval(callback, ms);

		onDestroy(() => {
			clearInterval(interval);
		});
	};
	const startCount = () => {
		isStart = !isStart;
		currentTurn++;
		$timer *= turnLength;
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
				isStart = !isStart;
				$timer = originalTime;
				Finish();
			}
			if ($timer < 0) {
				clearInterval(interval);
			}
		}, 1000);
	};
	const Finish = () => {
		isFinish = true;
		setTimeout(() => {
			isFinish = false;
		}, 4000);
	};
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: minname = minutes > 1 ? 'mins' : 'min';
	$: turnName = turns > 1 ? 'turns' : 'turn';
	$: currentTurnName = currentTurn > 1 ? 'turns' : 'turn';
</script>

<div class="">
	{#if isStart}
		{#if !isBreak}
			<div>
				<p>
					remaining time: {#if minutes >= 1} {minutes} {minname} and {/if}
					{seconds} seconds of {currentTurn}/{turns}
					{turnName}
				</p>
			</div>
		{:else}
			<div>
				<p>
					remaining time: {#if minutes >= 1} {minutes} {minname} and {/if}
					{seconds} seconds of break
				</p>
			</div>
		{/if}
		<div class="w-full bg-gray-200 h-5 mb-6">
			<div
				class="bg-blue-600 h-5"
				style="width:{($timer / (isBreak ? breakTime : originalTime)) * 100}%"
			/>
		</div>
	{:else if isFinish}
		<div>
			<p>Congrats</p>
		</div>
	{:else}
		<div class="flex bg-red-500 flex-col">
			<h1 class="">Welcome to Pomodoro app</h1>
			<button on:click={startCount}>Start timer</button>
			<div class="flex flex-row">
				<input type="number" bind:value={turnLength} class="" />
				<input type="number" bind:value={turns} />
				<input type="number" bind:value={breakTime} />
			</div>
		</div>
	{/if}
</div>
