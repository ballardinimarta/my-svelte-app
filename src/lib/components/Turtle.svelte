<script lang="ts">
	import { onMount } from 'svelte';
	import { T, useTask } from '@threlte/core';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js';

	export let position: [number, number, number] = [0, 0, 0];
	export let rotation: [number, number, number] = [0, 0, 0];
	export let scale: number = 1;

	let sceneRef: THREE.Group;
	let elapsedTime = 0;
	let loaded = false;
	let gltfScene: THREE.Object3D;
	let mixer: THREE.AnimationMixer | null = null;

	onMount(() => {
		const dracoLoader = new DRACOLoader();
		dracoLoader.setDecoderPath('https://www.gstatic.com/draco/versioned/decoders/1.5.6/');

		const loader = new GLTFLoader();
		loader.setDRACOLoader(dracoLoader);

		loader.load(
			'/model_52a_-_kemps_ridley_sea_turtle_no_id-transformed.glb',
			(gltf) => {
				if (gltf.scene) {
					gltfScene = gltf.scene;
					sceneRef = gltf.scene;

					// Set up animation mixer
					if (gltf.animations && gltf.animations.length > 0) {
						mixer = new THREE.AnimationMixer(gltf.scene);
						mixer.timeScale = 0.5;

						// Find and play the "Swim Cycle" animation
						const swimAnimation = gltf.animations.find((clip) => clip.name === 'Swim Cycle');
						if (swimAnimation) {
							const action = mixer.clipAction(swimAnimation);
							action.play();
						} else if (gltf.animations[0]) {
							// Fallback to first animation if "Swim Cycle" not found
							const action = mixer.clipAction(gltf.animations[0]);
							action.play();
						}
					}

					// Fix missing textures from KHR_materials_pbrSpecularGlossiness extension
					const materials = gltf.parser?.json?.materials;

					// Load all textures using getDependencies (handles embedded textures properly)
					gltf.parser
						.getDependencies('texture')
						.then((loadedTextures: THREE.Texture[]) => {
							gltfScene.traverse((child) => {
								if (child instanceof THREE.Mesh && child.material) {
									const mat = child.material as THREE.MeshStandardMaterial;
									const materialName = mat.name;

									// Find the corresponding material definition
									const matDef = materials?.find((m: any) => m.name === materialName);

									if (matDef?.extensions?.KHR_materials_pbrSpecularGlossiness) {
										const ext = matDef.extensions.KHR_materials_pbrSpecularGlossiness;

										// Apply diffuse texture as base color map
										if (ext.diffuseTexture) {
											mat.map = loadedTextures[ext.diffuseTexture.index];
											mat.map.colorSpace = THREE.SRGBColorSpace;
											mat.map.needsUpdate = true;
										}

										// Apply diffuse factor as color
										if (ext.diffuseFactor) {
											mat.color.setRGB(
												ext.diffuseFactor[0],
												ext.diffuseFactor[1],
												ext.diffuseFactor[2]
											);
											if (ext.diffuseFactor[3] !== undefined) {
												mat.opacity = ext.diffuseFactor[3];
												mat.transparent = ext.diffuseFactor[3] < 1;
											}
										}

										// Convert glossiness to roughness
										if (ext.glossinessFactor !== undefined) {
											mat.roughness = 1.0 - ext.glossinessFactor;
										}

										// Set metalness to 0 (specular-glossiness is non-metallic workflow)
										mat.metalness = 0;

										mat.needsUpdate = true;
									}

									child.castShadow = true;
									child.receiveShadow = true;
								}
							});
						})
						.catch((error: any) => {
							console.error('Error loading textures:', error);
						});
				}
				loaded = true;
			},
			undefined,
			(error) => {
				console.error('Error loading turtle model:', error);
				loaded = true;
			}
		);
	});

	useTask((delta: number) => {
		elapsedTime += delta;

		// Update animation mixer
		if (mixer) {
			mixer.update(delta);
		}

		// Apply rotation animation
		if (sceneRef) {
			sceneRef.rotation.z = Math.sin(elapsedTime / 4) / 2;
		}
	});
</script>

{#if loaded && gltfScene}
	<T is={gltfScene} bind:ref={sceneRef} {position} {rotation} {scale} />
{:else}
	<!-- Fallback simple turtle -->
	<T.Group {position} {rotation} {scale}>
		<T.Mesh>
			<T.SphereGeometry args={[0.02, 16, 16]} />
			<T.MeshStandardMaterial color="#4a7c59" />
		</T.Mesh>
	</T.Group>
{/if}
