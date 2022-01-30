<script>
	import '../app.css';
</script>

<svelte:head>
	<title>PomodoroApp</title>
	<meta
		name="description"
		content="Pomodoro app made for studying and improving your grades or
	 help you learn new things in a matter of minutes."
	/>
</svelte:head>

<div class="h-full w-screen min-h-screen bg-lightpurple">
	<div class="grid justify-items-center items-center h-screen">
		<slot />
		<div class="mt-auto text-purple-200 w-full text-center justify-center">
			<a href="https://github.com/Artuyees">Artur Kuciński | 2022</a>
		</div>
	</div>
</div>
