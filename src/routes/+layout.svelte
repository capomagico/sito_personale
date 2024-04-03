<script lang="ts">
	import Navbar from '$components/Navbar.svelte';
	import '../styles.css';
	import { onMount } from 'svelte';

	let x = 0;
	let y = 0;
	let isClickable = false;
	let cursorcontainer = 'custom-cursor';
	function updateMousePosition(event) {
		x = event.clientX;
		y = event.clientY;
	}
	function checkIfClickable(element) {
		return (
			(['A', 'BUTTON', 'INPUT'].includes(element.tagName) &&
				(element.hasAttribute('href') || ['button', 'submit'].includes(element.type))) ||
			element.onclick != null
		);
	}
	function updateCursorStatus() {
		cursorcontainer = isClickable ? 'custom-cursor-click' : 'custom-cursor';
	}
	function handleMouseOver(event) {
		isClickable = checkIfClickable(event.target);
		updateCursorStatus();
	}

	function handleMouseOut() {
		isClickable = false;
		updateCursorStatus();
	}
	onMount(() => {
		document.addEventListener('mousemove', updateMousePosition);
		document.addEventListener('mouseover', handleMouseOver);
		document.addEventListener('mouseout', handleMouseOut);

		return () => {
			document.removeEventListener('mousemove', updateMousePosition);
			document.removeEventListener('mouseover', handleMouseOver);
			document.removeEventListener('mouseout', handleMouseOut);
		};
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

<div class="track">
	<div style="left: {x}px; top: {y}px;">
		<div class={cursorcontainer}></div>
	</div>
</div>

<style>
	:global(*) {
		cursor: none;
	}
	.track > div {
		position: absolute;
		pointer-events: none;
	}
	.custom-cursor {
		width: 100px;
		height: 100px;
		background-image: url('src/components/cursore_0deg.svg');
		transform: scale(0.666) rotate(-24.159deg) translateY(-52px) translateX(-38px);
		transform-origin: center;
		pointer-events: none;
	}

	.custom-cursor-click {
		width: 100px;
		height: 100px;
		background-image: url('src/components/cursore_click_0deg.svg');
		transform: scale(0.666) rotate(-24.159deg) translateY(-52px) translateX(-30px);
		transform-origin: center;
		pointer-events: none;
	}
</style>
