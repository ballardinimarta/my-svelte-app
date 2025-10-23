<script lang="ts">
	import { Canvas, T } from '@threlte/core';
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import AnimatedBox from './AnimatedBox.svelte';

	const length = 35;
	const colors = ['#A2CCB6', '#FCEEB5', '#EE786E', '#e0feff', 'lightpink', 'lightblue'];

	const data = Array.from({ length }, () => ({
		args: [0.1 + Math.random() * 9, 0.1 + Math.random() * 9, 10] as [number, number, number]
	}));

	const random = (i: number) => {
		const r = Math.random();
		return {
			position: [100 - Math.random() * 200, 100 - Math.random() * 200, i * 1.5] as [
				number,
				number,
				number
			],
			color: colors[Math.round(Math.random() * (colors.length - 1))],
			scale: [1 + r * 14, 1 + r * 14, 1] as [number, number, number],
			rotation: [0, 0, THREE.MathUtils.degToRad(Math.round(Math.random()) * 45)] as [
				number,
				number,
				number
			]
		};
	};

	const boxes = Array.from({ length }, (_, i) => {
		const initial = random(i);
		return {
			...initial,
			update: null as any
		};
	});

	onMount(() => {
		const animate = () => {
			boxes.forEach((box, i) => {
				const newState = random(i);
				setTimeout(() => {
					if (box.update) {
						box.update.position(newState.position);
						box.update.scale(newState.scale);
						box.update.rotation(newState.rotation);
						box.update.color(newState.color);
					}
				}, i * 40);
			});
		};

		animate();
		const interval = setInterval(animate, 3000);
		return () => clearInterval(interval);
	});
</script>

<div class="absolute inset-0 bg-white" style="width: 100%; height: 100%;">
	<Canvas shadows colorManagementEnabled={false}>
		<T.PerspectiveCamera makeDefault position={[0, 0, 100]} fov={100} />
		<T.PointLight intensity={0.5} />
		<T.AmbientLight intensity={1.85} />
		<T.SpotLight
			castShadow
			intensity={0.2}
			angle={Math.PI / 7}
			position={[150, 150, 250]}
			penumbra={1}
			shadow.mapSize.width={2048}
			shadow.mapSize.height={2048}
		/>

		{#each boxes as box, index (index)}
			<AnimatedBox
				args={data[index].args}
				initialPosition={box.position}
				initialScale={box.scale}
				initialRotation={box.rotation}
				initialColor={box.color}
				onReady={(update) => (box.update = update)}
			/>
		{/each}
	</Canvas>
</div>
