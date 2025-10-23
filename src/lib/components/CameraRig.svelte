<script lang="ts">
	import { useTask, useThrelte } from '@threlte/core';
	import { onMount } from 'svelte';

	let { radius = 20 } = $props();

	const { camera, renderer } = useThrelte();

	let pointerX = $state(0);
	let pointerY = $state(0);

	onMount(() => {
		const canvas = renderer.domElement;

		const handlePointerMove = (e: PointerEvent) => {
			const rect = canvas.getBoundingClientRect();
			pointerX = ((e.clientX - rect.left) / rect.width) * 2 - 1;
			pointerY = -((e.clientY - rect.top) / rect.height) * 2 + 1;
		};

		canvas.addEventListener('pointermove', handlePointerMove);

		return () => {
			canvas.removeEventListener('pointermove', handlePointerMove);
		};
	});

	useTask((delta) => {
		const targetX = Math.sin(pointerX) * radius;
		const targetY = Math.atan(pointerY) * radius;
		const targetZ = Math.cos(pointerX) * radius;

		// Smooth camera movement
		$camera.position.x += (targetX - $camera.position.x) * 0.05;
		$camera.position.y += (targetY - $camera.position.y) * 0.05;
		$camera.position.z += (targetZ - $camera.position.z) * 0.05;

		$camera.lookAt(0, 0, 0);
	});
</script>
