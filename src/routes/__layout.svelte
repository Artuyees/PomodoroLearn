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

<div class="h-screen w-screen bg-lightpurple">
	<div class="grid justify-items-center items-center h-screen">
		<slot />
	</div>
</div>
