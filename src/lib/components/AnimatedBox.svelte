<script lang="ts">
	import { T } from '@threlte/core';
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';
	import { onMount } from 'svelte';

	interface Props {
		args: [number, number, number];
		initialPosition: [number, number, number];
		initialScale: [number, number, number];
		initialRotation: [number, number, number];
		initialColor: string;
		onReady: (update: {
			position: (data: [number, number, number]) => void;
			scale: (data: [number, number, number]) => void;
			rotation: (data: [number, number, number]) => void;
			color: (data: string) => void;
		}) => void;
	}

	let { args, initialPosition, initialScale, initialRotation, initialColor, onReady }: Props =
		$props();

	const position = tweened(initialPosition, { duration: 2000, easing: cubicOut });
	const scale = tweened(initialScale, { duration: 2000, easing: cubicOut });
	const rotation = tweened(initialRotation, { duration: 2000, easing: cubicOut });
	const color = tweened(initialColor, { duration: 2000, easing: cubicOut });

	onMount(() => {
		onReady({
			position: (data: [number, number, number]) => position.set(data),
			scale: (data: [number, number, number]) => scale.set(data),
			rotation: (data: [number, number, number]) => rotation.set(data),
			color: (data: string) => color.set(data)
		});
	});
</script>

<T.Mesh position={$position} scale={$scale} rotation={$rotation} castShadow receiveShadow>
	<T.BoxGeometry {args} />
	<T.MeshStandardMaterial color={$color} roughness={0.75} metalness={0.5} />
</T.Mesh>
