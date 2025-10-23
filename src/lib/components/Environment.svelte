<script lang="ts">
	import { onMount } from 'svelte';
	import { useThrelte } from '@threlte/core';
	import * as THREE from 'three';

	const { scene, renderer } = useThrelte();

	onMount(() => {
		if (!renderer || !scene) return;

		// Create a simple procedural environment map
		const pmremGenerator = new THREE.PMREMGenerator(renderer);
		pmremGenerator.compileEquirectangularShader();

		// Create environment scene matching React's Lightformer setup
		const envScene = new THREE.Scene();
		envScene.background = new THREE.Color(0xc6e5db);

		// Rotate entire environment (matching React: rotation={[-Math.PI / 3, 0, 0]})
		const envGroup = new THREE.Group();
		envGroup.rotation.x = -Math.PI / 3;
		envScene.add(envGroup);

		// Main top lightformer from camera direction
		const mainLight = new THREE.DirectionalLight(0xffffff, 3);
		mainLight.position.set(15, 5, -9);
		envGroup.add(mainLight);

		// Circle lightformers array from camera direction
		const positions = [2, 0, 2, 0, 2, 0, 2, 0];
		positions.forEach((x, i) => {
			const circleLight = new THREE.DirectionalLight(0xffffff, 2);
			circleLight.position.set(x + 12, 4, i * 4);
			envGroup.add(circleLight);
		});

		// Side lightformers from camera direction
		const sideLight1 = new THREE.DirectionalLight(0xe8f4f8, 3);
		sideLight1.position.set(15, 1, -1);
		envGroup.add(sideLight1);

		const sideLight2 = new THREE.DirectionalLight(0xe8f4f8, 3);
		sideLight2.position.set(15, 1, 1);
		envGroup.add(sideLight2);

		// Fill lights from camera side
		const fillLight1 = new THREE.DirectionalLight(0xb3e0f2, 2);
		fillLight1.position.set(20, -3, 0);
		envGroup.add(fillLight1);

		const fillLight2 = new THREE.DirectionalLight(0xb3e0f2, 2);
		fillLight2.position.set(12, 5, 0);
		envGroup.add(fillLight2);

		// Generate environment map (use smaller sigma to avoid clipping)
		const renderTarget = pmremGenerator.fromScene(envScene, 0.01);

		// Apply to main scene
		scene.environment = renderTarget.texture;

		// Cleanup
		return () => {
			renderTarget.dispose();
			pmremGenerator.dispose();
		};
	});
</script>
