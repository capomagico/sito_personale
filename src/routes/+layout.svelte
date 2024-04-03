<script lang="ts">
	import Navbar from '$components/Navbar.svelte';
	import '../styles.css';

	let cursorX = 0;
	let cursorY = 0;
	let isClickable = false;

	function handleMouseMove(event) {
		cursorX = event.clientX;
		cursorY = event.clientY;
	}

	function handleMouseEnter() {
		isClickable = true;
	}

	function handleMouseLeave() {
		isClickable = false;
	}

	onMount(() => {
		document.addEventListener('mousemove', handleMouseMove);
	});

	onDestroy(() => {
		document.removeEventListener('mousemove', handleMouseMove);
	});
</script>

<link
	href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
	rel="stylesheet"
	integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH"
	crossorigin="anonymous"
/>
<link rel="stylesheet" href="https://unpkg.com/tailwindcss@3.4.1/src/css/preflight.css" />

<slot />

<div class="custom-cursor {isClickable ? 'custom-cursor-click' : ''}" />

<style>
	.custom-cursor {
		position: fixed;
		top: 0;
		left: 0;
		width: 100px; /* Imposta la larghezza desiderata */
		height: 100px; /* Imposta l'altezza desiderata */
		/* Aggiungi il resto delle tue proprietà CSS personalizzate */
		background-image: url('src/components/cursore_0deg.svg');
		background-size: cover;
		transform: scale(0.666) rotate(-24.159deg);
		transform-origin: center;
		pointer-events: none;
		z-index: 9999;
	}

	.custom-cursor-click {
		width: 100%;
		height: 100%;
		background-image: url('src/components/cursore_click_0deg.svg');
		background-size: cover;
		transform: scale(0.666) rotate(-24.159deg) translateY(-90px) translateX(45px);
		transform-origin: center;
	}
</style>
