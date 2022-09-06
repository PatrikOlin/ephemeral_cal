<script>
 	import Calendar from './components/Calendar.svelte'
	export let name;
 	const today = new Date();
</script>

<main>
	<div>
		<h1>ephemeral calendar</h1>
		<Calendar {today} />
	</div>
	<footer>
		Made with 🫀 by klatter
	</footer>
</main>

<style>
	main {
		text-align: center;
		padding: 0 1rem;
		margin: 0 auto;
		min-height: 100vh;
		display: flex;
		flex-direction: column;
	}

	footer {
		margin-top: auto;
		padding-bottom: 3rem;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
