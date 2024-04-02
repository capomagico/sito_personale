<script lang="ts">
	import Navbar from '$components/Navbar.svelte';
	import '../styles.css';

	document.addEventListener('mousemove', (event) => {
		const cursorContainer = document.getElementById('custom-cursor-container');
		const offsetX = 27; // Adjust this value based on your cursor's design
		const offsetY = 18; // Adjust this value based on your cursor's design
		const x = event.clientX - offsetX;
		const y = event.clientY - offsetY;
		const transform = `translate(${x}px, ${y}px)`;
		cursorContainer.style.transform = transform;
	});
</script>

<link
	href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
	rel="stylesheet"
	integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH"
	crossorigin="anonymous"
/>
<link rel="stylesheet" href="https://unpkg.com/tailwindcss@3.4.1/src/css/preflight.css" />

<div id="custom-cursor-container">
	<div id="custom-cursor"></div>
</div>

<slot />

<style>
	/* Add the following CSS to set the custom cursor */
	:global(body) {
		cursor: none;
	}

	#custom-cursor-container {
		position: fixed;
		top: 0;
		left: 0;
		width: 70px; /* Scaled up size */
		height: 70px; /* Scaled up size */
		transform-origin: center;
		pointer-events: none;
	}

	#custom-cursor {
		width: 100%;
		height: 100%;
		background-image: url('src/components/cursore_0deg.svg');
		background-size: cover;
		transform: scale(0.666) rotate(-24.159deg); /* Scale down to original size and apply rotation */
		transform-origin: center;
	}
</style>
