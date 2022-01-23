<script>
	import { tweened } from 'svelte/motion';
	let interval;
	let isBreak = false;
	let isStart = false;
	let isFinish = false;
	let isPaused = false;
	let isSoundPaused = true;
	let isClickedOnce = false;
	let breakTime = 5;
	let currentTurn = 0;
	let turns = 2;
	let turnLength = 20;
	let originalTime = turnLength * 60;
	let timer = tweened();

	const handleTimer = () => {
		$timer--;
		console.log($timer, 'handle timer');
		if (currentTurn > turns) {
			clearInterval(interval);
			currentTurn = 0;
			isStart = !isStart;
			$timer = originalTime;
			Finish();
		}
		if (Math.floor($timer) == 0 && !isBreak) {
			isBreak = !isBreak;
			isSoundPaused = !isSoundPaused;
			$timer = breakTime;
		}
		if (Math.floor($timer) == 0 && isBreak) {
			currentTurn++;
			$timer = originalTime;
			isBreak = !isBreak;
			isSoundPaused = !isSoundPaused;
		}

		if ($timer < 0) {
			breakTime /= 60;
			clearInterval(interval);
		}
	};

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
			handleTimer();
		}, 1000);
	};

	const pauseTimer = () => {
		isPaused = !isPaused;
		if (isPaused) {
			clearInterval(interval);
		} else {
			onInterval(() => {
				handleTimer();
			}, 1000);
		}
	};
	const cancelTimer = () => {
		if (isClickedOnce) {
			clearInterval(interval);
			breakTime /= 60;
			currentTurn = 0;
			isBreak = false;
			isStart = false;
			isFinish = false;
			isClickedOnce = !isClickedOnce;
		} else {
			isClickedOnce = true;
			setTimeout(() => {
				isClickedOnce = !isClickedOnce;
			}, 3000);
		}
	};
	const Finish = () => {
		isFinish = true;
		setTimeout(() => {
			isFinish = false;
		}, 4000);
	};

	const skipTurn = () => {
		clearInterval(interval);
		$timer = 1;
		onInterval(() => {
			handleTimer();
		}, 1000);
	};
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);
	$: turnName = turns > 1 ? 'turns' : 'turn';
</script>

<audio bind:paused={isSoundPaused} src="http://soundbible.com/grab.php?id=2218&type=mp3"
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
				{minutes < 10 ? '0' : ''}{minutes}:{seconds < 10 ? '0' : ''}{seconds}
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
		<div class="grid {isClickedOnce || isPaused ? 'grid-cols-1' : 'grid-cols-3'} gap-4">
			<button
				on:click={cancelTimer}
				class=" w-32 h-12 {isPaused ? 'hidden' : ''} {isClickedOnce
					? 'col-span-'
					: ''} transition ease-in duration-300 rounded-lg bg-white border-black border-2 hover:bg-red-800 hover:scale-105 text-black hover:text-white hover:border-0 text-lg"
				><strong>{!isClickedOnce ? 'cancel Timer' : 'Are you sure?'}</strong></button
			>
			<button
				on:click={pauseTimer}
				class=" {isClickedOnce
					? 'hidden'
					: ''} w-32 h-12 transition ease-in duration-300 rounded-lg bg-white border-black border-2 {!isPaused
					? 'hover:bg-blue-800'
					: 'hover:bg-green-800'} hover:scale-105 text-black hover:text-white hover:border-0 text-lg"
				><strong>{!isPaused ? 'pause' : 'play'}</strong></button
			>
			<button
				on:click={skipTurn}
				class=" w-32 h-12 {isPaused || isClickedOnce
					? 'hidden'
					: ''} transition ease-in duration-300 rounded-lg bg-white border-black border-2 hover:bg-blue-800 hover:scale-105 text-black hover:text-white hover:border-0 text-lg"
				><strong>Skip Turn</strong></button
			>
		</div>
	</div>
{:else if isFinish}
	<div class="">
		<p class="text-white text-7xl"><strong>Congratulations</strong></p>
	</div>
{:else}
	<div class="flex flex-col items-center rounded-lg bg-white shadow-xl p-8">
		<h1 class="text-4xl m-8 ">Welcome to Pomodoro app</h1>
		<div class="flex md:flex-row flex-col text-xl mb-8">
			<div class="text-center m-2">
				<label
					><input
						id="turnTime"
						type="number"
						bind:value={turnLength}
						class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
					/></label
				>

				<p class="">Turn time? (minutes)</p>
			</div>

			<div class="text-center m-2">
				<label>
					<input
						id="turnsNumber"
						type="number"
						bind:value={turns}
						class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
					/>
				</label>

				<p>How many turns?</p>
			</div>
			<div class="text-center m-2">
				<label>
					<input
						id="breakTime"
						type="number"
						bind:value={breakTime}
						class=".appearance-none h-16 text-center m-2 w-16 bg-white border-blackpurple border-2 rounded-lg"
					/>
				</label>

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
<div class="absolute bottom-0 text-purple-200 w-full text-center justify-center">
	<a href="https://github.com/Artuyees">Artur Kuciński | 2022</a>
</div>

<style>
	input[type='number']::-webkit-inner-spin-button,
	input[type='number']::-webkit-outer-spin-button {
		-webkit-appearance: none;
		margin: 0;
	}
</style>
