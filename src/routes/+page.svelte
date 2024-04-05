<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
	import { UnrealBloomPass } from 'three/examples/jsm/postprocessing/UnrealBloomPass.js';
	import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer.js';
	import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass.js';

	let container;

	onMount(() => {
		let scene, camera, renderer, globo;
		let controls; // Variabile per OrbitControls
		let composer, renderPass, bloomPass;
		const raycaster = new THREE.Raycaster();
		const mouse = new THREE.Vector2();

		init();
		initPostProcessing();
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
			camera.position.y = 5;

			// Renderer con alta risoluzione
			renderer = new THREE.WebGLRenderer({ antialias: false });
			renderer.setSize(container.clientWidth, container.clientHeight);
			renderer.setPixelRatio(window.devicePixelRatio);
			container.appendChild(renderer.domElement);

			// Caricamento dell'oggetto 3D con materiale modificato
			const objLoader = new OBJLoader();
			objLoader.load(
				'https://raw.githubusercontent.com/capomagico/sito_personale/main/src/components/globo.obj',
				function (object) {
					object.traverse(function (child) {
						if (child instanceof THREE.Mesh) {
							child.material = new THREE.MeshStandardMaterial({
								color: 0xf27200, // Colore dell'oggetto
								emissive: 0xf27200, // Colore della luce emessa dall'oggetto
								emissiveIntensity: 1 // Intensità della luce emessa
							});
						}
					});
					globo = object;
					scene.add(globo);

					// Creazione delle sferette con identificatori unici
					const sferaGeometry = new THREE.SphereGeometry(0.2, 32, 32); // Geometria della sferetta
					const sferaMaterial = new THREE.MeshStandardMaterial({
						color: 0xffffff, // Colore #f27200
						emissive: 0xffffff, // Colore emissivo
						emissiveIntensity: 1 // Intensità emissiva
					});

					// Posizioni iniziali delle sferette rispetto al globo
					const posizioni = [
						{ x: 4.12, y: 0, z: 0, id: 'sferetta1' },
						{ x: -2.1, y: 1.55, z: 3.1, id: 'sferetta2' },
						{ x: 0, y: -1.55, z: -3.78, id: 'sferetta3' }
					];

					posizioni.forEach((pos) => {
						let sfera = new THREE.Mesh(sferaGeometry, sferaMaterial);
						sfera.position.set(pos.x, pos.y, pos.z);
						sfera.name = pos.id; // Assegna l'identificatore unico
						globo.add(sfera); // Aggiungi la sfera come figlio del globo
					});
				}
			);

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

			// Aggiungi gli event listeners
			container.addEventListener('click', onClick);
			container.addEventListener('mousemove', onMouseMove);
		}

		function initPostProcessing() {
			composer = new EffectComposer(renderer);
			renderPass = new RenderPass(scene, camera);
			composer.addPass(renderPass);

			const performanceThreshold = 30; // Soglia di tempo di frame in millisecondi (es. 30ms)
			let bloomFactor = 1; // Inizia con la massima qualità

			function adjustBloomQuality() {
				const deltaTime = renderer.info.render.frame * 0.001; // Calcola il tempo di frame
				if (deltaTime > performanceThreshold) {
					bloomFactor = Math.min(bloomFactor + 0.1, 4); // Aumenta il fattore per ridurre la qualità
				} else {
					bloomFactor = Math.max(bloomFactor - 0.1, 1); // Diminuisce il fattore per migliorare la qualità
				}

				// Aggiorna l'effetto bloom con il nuovo fattore
				bloomPass = new UnrealBloomPass(
					new THREE.Vector2(window.innerWidth / bloomFactor, window.innerHeight / bloomFactor),
					1.5,
					0.4,
					0.85
				);
				bloomPass.threshold = 0.21;
				bloomPass.strength = 1.2 / bloomFactor; // Adatta l'intensità in base al fattore
				bloomPass.radius = 0.5 / bloomFactor; // Adatta il raggio in base al fattore
				composer.addPass(bloomPass);
			}

			// Chiama questa funzione ogni volta che vuoi aggiustare la qualità dell'effetto bloom
			adjustBloomQuality();
		}

		function animate() {
			requestAnimationFrame(animate);
			globo.rotation.y += 0.001;
			controls.update();
			composer.render(); // Usa il composer per il rendering
		}

		function onClick(event) {
			mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
			mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
			raycaster.setFromCamera(mouse, camera);
			const intersects = raycaster.intersectObjects(globo.children);

			if (intersects.length > 0) {
				const sferettaCliccata = intersects[0].object;

				// Controlla quale sferetta è stata cliccata basandoti sull'identificatore
				switch (sferettaCliccata.name) {
					case 'sferetta1':
						window.location.href = 'https://tuo.sito/pagina1';
						break;
					case 'sferetta2':
						window.location.href = 'https://tuo.sito/pagina2';
						break;
					case 'sferetta3':
						window.location.href = 'https://tuo.sito/pagina3';
						break;
					default:
						console.log('Sferetta non riconosciuta');
				}
			}
		}

		function onMouseMove(event) {
			mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
			mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
			raycaster.setFromCamera(mouse, camera);
			const intersects = raycaster.intersectObjects(globo.children, true);

			// Ripristina lo stato originale di tutte le sferette
			globo.children.forEach((child) => {
				if (child.name.startsWith('sferetta')) {
					child.material.color.set(0xffffff); // Colore originale
					child.material.emissive.set(0xffffff); // Emissività originale
					child.scale.set(1, 1, 1); // Scala originale
				}
			});

			if (intersects.length > 0) {
				const sferettaIntersecata = intersects.find((intersect) =>
					intersect.object.name.startsWith('sferetta')
				);

				if (sferettaIntersecata) {
					// Qui puoi aggiungere condizioni specifiche per ogni sferetta
					switch (sferettaIntersecata.object.name) {
						case 'sferetta1':
							// Azioni specifiche per sferetta1
							sferettaIntersecata.object.scale.set(1.3, 1.3, 1.3); // Scala leggermente più ingrandita
							break;
						case 'sferetta2':
							// Azioni specifiche per sferetta2
							sferettaIntersecata.object.scale.set(1.3, 1.3, 1.3); // Scala leggermente più ingrandita
							break;
						case 'sferetta3':
							// Azioni specifiche per sferetta3
							sferettaIntersecata.object.scale.set(1.3, 1.3, 1.3); // Scala leggermente più ingrandita
							break;
						// Aggiungi più casi se necessario
					}

					// Aggiungi una transizione veloce per l'ingrandimento
					sferettaIntersecata.object.material.transparent = true;
					sferettaIntersecata.object.material.opacity = 0.5; // Inizia con opacità ridotta
					new TWEEN.Tween(sferettaIntersecata.object.material)
						.to({ opacity: 1 }, 200) // Transizione a opacità piena in 200ms
						.start();
				}
			}
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
