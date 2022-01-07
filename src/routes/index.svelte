<script>
	import { tweened } from 'svelte/motion';
	let interval;
	let isBreak = false;
	let breakTime = 5;
	let isStart = false;
	let currentTurn = 0;
	let isFinish = false;
	let turns = 2;
	let isPaused = true;
	let turnLength = 20;
	let originalTime = turnLength * 60;
	let timer = tweened();

	const onInterval = (callback, ms) => {
		interval = setInterval(callback, ms);
	};
	const startCount = () => {
		originalTime = turnLength * 60;
		$timer = turnLength * 60;
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
				isPaused = !isPaused;
				$timer = breakTime;
			}
			if (Math.floor($timer) == 0 && isBreak) {
				currentTurn++;
				$timer = originalTime;
				isBreak = !isBreak;
				isPaused = !isPaused;
			}

			if ($timer < 0) {
				breakTime /= 60;
				clearInterval(interval);
			}
		}, 1000);
	};

	const cancelTimer = () => {
		clearInterval(interval);
		isBreak = false;
		isStart = false;
		isFinish = false;
	};
	const Finish = () => {
		isFinish = true;
		setTimeout(() => {
			isFinish = false;
		}, 4000);
	};
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: turnName = turns > 1 ? 'turns' : 'turn';
</script>

<audio bind:paused={isPaused} src="http://soundbible.com/grab.php?id=2218&type=mp3"
	><!-- Sound "Service Bell Help" by  Daniel Simion from soundbible.com, licensed under Attribution 3.0  --></audio
>

{#if isStart}
	<div
		class="grid grid-cols-1 gap-4 justify-items-center shadow-xl p-4 w-1/3 text-2xl bg-white rounded-lg text-black min-w-fit "
	>
		{#if !isBreak}
			<div class="text-7xl">
				{minutes < 10 ? '0' : ''}{minutes}:{seconds < 10 ? '0' : ''}{seconds}
			</div>
			{currentTurn}/{turns}
			{turnName}
		{:else}
			<div class="text-7xl">
				{minutes < 10 ? '0' : ''}{minutes}:{seconds}
			</div>
		{/if}
		<div class="w-full mx-4 rounded-full overflow-hidden bg-gray-200 h-8">
			<div
				class="bg-gradient-to-t {isBreak
					? 'from-lime-900  to-lime-600'
					: 'from-red-900  to-red-600'} rounded-full h-8"
				style="width:{($timer / (isBreak ? breakTime : originalTime)) * 100}%"
			/>
		</div>
		<button
			on:click={cancelTimer}
			class=" w-32 h-12 transition ease-in duration-300 rounded-lg bg-white border-black border-2 hover:bg-red-800 hover:scale-105 text-black hover:text-white hover:border-0 text-lg"
			><strong>stop timer</strong></button
		>
	</div>
{:else if isFinish}
	<div class="">
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
