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
	let timer = tweened(turnLength * 60);
	const onInterval = (callback, ms) => {
		interval = setInterval(callback, ms);
	};
	const startCount = () => {
		isStart = !isStart;
		currentTurn++;
		breakTime *= 60;
		onInterval(() => {
			$timer--;
			if (currentTurn > turns) {
				clearInterval(interval);
				currentTurn = 0;
				isStart = !isStart;
				$timer = originalTime;
				Finish();
			}
			if (Math.floor($timer) == 0 && !isBreak) {
				isBreak = !isBreak;
				$timer = breakTime;
			}
			if (Math.floor($timer) == 0 && isBreak) {
				currentTurn++;
				$timer = originalTime;
				isBreak = !isBreak;
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
</script>

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
		<p class="text-white text-2xl">Congratulations</p>
	</div>
{:else}
	<div class="flex flex-col items-center rounded-lg bg-white shadow-xl p-8">
		<h1 class="text-4xl m-8 ">Welcome to Pomodoro app</h1>
		<div class="flex md:flex-row flex-col text-xl mb-8">
			<div class="text-center m-2">
				<input
					type="number"
					bind:value={turnLength}
					class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
				/>
				<p class="">Turn time? (minutes)</p>
			</div>

			<div class="text-center m-2">
				<input
					type="number"
					bind:value={turns}
					class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
				/>
				<p>How many turns?</p>
			</div>
			<div class="text-center m-2">
				<input
					type="number"
					bind:value={breakTime}
					class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
				/>
				<p>Break time? (minutes)</p>
			</div>
		</div>
		<button
			on:click={startCount}
			class=" w-44 h-16 transition ease-in duration-300 bg-confirm hover:bg-confirmhover hover:scale-105 text-white rounded-lg text-lg"
			><strong>Start timer</strong></button
		>
	</div>
{/if}

<style>
	input[type='number']::-webkit-inner-spin-button,
	input[type='number']::-webkit-outer-spin-button {
		-webkit-appearance: none;
		margin: 0;
	}
</style>
