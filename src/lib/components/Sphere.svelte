<script lang="ts">
	import { T } from '@threlte/core';
	import { Float } from '@threlte/extras';

	export let position: [number, number, number] = [0, 0, 0];
	export let scale: number = 1;
	export let speed: number = 0.1;
	export let color: string = 'white';
</script>

<Float rotationIntensity={40} floatIntensity={20} speed={speed / 2}>
	<T.Mesh {position} {scale}>
		<T.SphereGeometry args={[1, 64, 64]} />
		<T.MeshBasicMaterial {color} depthTest={false} />
	</T.Mesh>
</Float>
