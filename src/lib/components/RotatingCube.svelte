<script lang="ts">
	import { onMount } from 'svelte';
	import { T, useTask, useThrelte } from '@threlte/core';
	import { OrbitControls } from '@threlte/extras';
	import * as THREE from 'three';
	import cubeFace1 from '$lib/assets/cube-face-1.jpg';
	import cubeFace2 from '$lib/assets/cube-face-2.jpg';
	import cubeFace3 from '$lib/assets/cube-face-3.jpg';
	import cubeFace4 from '$lib/assets/cube-face-4.jpg';
	import cubeFace5 from '$lib/assets/cube-face-5.jpg';
	import cubeFace6 from '$lib/assets/cube-face-6.jpg';

	let meshRef: THREE.Mesh;
	let materials: THREE.MeshStandardMaterial[] = [];

	const { scene } = useThrelte();

	onMount(() => {
		const textureLoader = new THREE.TextureLoader();
		const texturePaths = [cubeFace1, cubeFace2, cubeFace3, cubeFace4, cubeFace5, cubeFace6];

		materials = texturePaths.map((path) => {
			const texture = textureLoader.load(path);
			return new THREE.MeshStandardMaterial({
				map: texture,
				roughness: 0.4,
				metalness: 0.2
			});
		});

		// Force update
		if (meshRef) {
			meshRef.material = materials;
		}
	});

	// Auto-rotate
	useTask((delta: number) => {
		if (meshRef) {
			meshRef.rotation.x += delta * 0.2;
			meshRef.rotation.y += delta * 0.3;
		}
	});
</script>

<T.PerspectiveCamera makeDefault position={[0, 0, 8]}>
	<OrbitControls enableZoom={true} enablePan={false} minDistance={5} maxDistance={15} />
</T.PerspectiveCamera>

<T.Mesh bind:ref={meshRef} castShadow receiveShadow material={materials}>
	<T.BoxGeometry args={[3, 3, 3]} />
</T.Mesh>

<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[5, 5, 5]} intensity={1} castShadow />
<T.PointLight position={[-5, -5, -5]} intensity={0.5} color="#9333ea" />
