<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

	let container;

	onMount(() => {
		let scene, camera, renderer, globo;
		let textureEquirec;
		let controls; // Variabile per OrbitControls

		init();
		animate();

		function init() {
			// Creazione della scena
			scene = new THREE.Scene();
			scene.background = new THREE.Color(0x000000);

			// Creazione della camera
			camera = new THREE.PerspectiveCamera(
				75,
				container.clientWidth / container.clientHeight,
				0.1,
				1000
			);
			camera.position.z = 9;

			// Renderer con alta risoluzione
			renderer = new THREE.WebGLRenderer({ antialias: true });
			renderer.setSize(container.clientWidth, container.clientHeight);
			renderer.setPixelRatio(window.devicePixelRatio);
			container.appendChild(renderer.domElement);

			// Environment Map ad alta risoluzione
			const loader = new THREE.TextureLoader();
			textureEquirec = loader.load(
				'https://github.com/capomagico/sito_personale/blob/a65618fe0f2d2bd0a95bdc3ab7fc834cc88ee824/src/components/Misty%20Cloudy%20Sky%208k.jpeg'
			);
			textureEquirec.mapping = THREE.EquirectangularReflectionMapping;
			scene.environment = textureEquirec;

			// Caricamento dell'oggetto 3D con materiale cromato
			const objLoader = new OBJLoader();
			objLoader.load('src/components/globo.obj', function (object) {
				object.traverse(function (child) {
					if (child instanceof THREE.Mesh) {
						child.material = new THREE.MeshStandardMaterial({
							color: 0xffffff,
							metalness: 1.0,
							roughness: 0.0,
							envMap: textureEquirec
						});
					}
				});
				globo = object;
				scene.add(globo);
			});

			// Luci
			const ambientLight = new THREE.AmbientLight(0xffffff);
			scene.add(ambientLight);

			const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
			directionalLight.position.set(0, 1, 0);
			scene.add(directionalLight);

			// Inizializzazione di OrbitControl con smorzamento
			controls = new OrbitControls(camera, renderer.domElement);
			controls.enableDamping = true; // Abilita lo smorzamento (inerzia)
			controls.dampingFactor = 0.05; // Fattore di smorzamento
			controls.enableZoom = false; // Disabilita la possibilità di zoomare
		}

		function animate() {
			requestAnimationFrame(animate);
			globo.rotation.y += 0.002;
			controls.update(); // Aggiorna i controlli per applicare lo smorzamento
			renderer.render(scene, camera);
		}
	});
</script>

<div class="homepage" bind:this={container}></div>

<style>
	.homepage {
		background-color: black;
		display: flex;
		height: 100vh;
		margin: 0;
		align-items: center;
		justify-content: center;
	}
</style>
