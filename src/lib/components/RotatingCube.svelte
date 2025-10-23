<script lang="ts">
	import { T } from '@threlte/core';
	import { Float, OrbitControls } from '@threlte/extras';
	import Aquarium from './Aquarium.svelte';
	import Turtle from './Turtle.svelte';
	import Sphere from './Sphere.svelte';
	import Environment from './Environment.svelte';
	import RendererConfig from './RendererConfig.svelte';

	const spheres: Array<[number, string, number, [number, number, number]]> = [
		[1, 'orange', 0.05, [-1.5, -0.5, -0.5]],
		[0.75, 'hotpink', 0.1, [-1.5, 1, -1]],
		[1.25, 'aquamarine', 0.2, [1.5, -1.5, 1]],
		[1.5, 'lightblue', 0.3, [-1.5, -1, -1.5]],
		[2, 'pink', 0.3, [-1.5, 1, -2]],
		[2, 'skyblue', 0.3, [-1.5, 1, -2]],
		[1.5, 'orange', 0.05, [-1.5, -0.5, -0.5]],
		[2, 'hotpink', 0.1, [-1.5, 1, -1]],
		[1.5, 'aquamarine', 0.2, [1.5, -1.5, 1]],
		[1.25, 'lightblue', 0.3, [-1.5, -1, -1.5]],
		[1, 'pink', 0.3, [-1.5, 1, -2]],
		[1, 'skyblue', 0.3, [-1.5, 1, -2]]
	];
</script>

<RendererConfig />
<Environment />

<!-- Background color matching React -->
<T.Color attach="background" args={['#c6e5db']} />

<T.PerspectiveCamera makeDefault position={[30, 0, -3]} fov={35} near={1} far={50}>
	<OrbitControls
		enableZoom={true}
		enablePan={false}
		minPolarAngle={0}
		maxPolarAngle={Math.PI / 2}
	/>
</T.PerspectiveCamera>

<Aquarium position={[0, 0.25, 0]} rotation={[0, Math.PI / 2, 0]} scale={1.5}>
	<Float rotationIntensity={2} floatIntensity={10} speed={2}>
		<Turtle position={[0, -0.5, -1]} rotation={[0, Math.PI, 0]} scale={15} />
	</Float>

	{#each spheres as [scale, color, speed, position]}
		<Sphere {scale} {color} {speed} {position} />
	{/each}
</Aquarium>

<!-- Ground plane for soft shadows -->
<T.Mesh rotation.x={-Math.PI / 2} position={[0, -5, 0]} receiveShadow>
	<T.CircleGeometry args={[30, 64]} />
	<T.ShadowMaterial opacity={0.15} color="#a7d5e6" />
</T.Mesh>

<!-- Enhanced Lighting System (matching React's multiple Lightformers) -->
<T.AmbientLight intensity={0.6} />

<!-- Main directional light with shadows (from camera side) -->
<T.DirectionalLight
	position={[15, 10, -5]}
	intensity={1.5}
	castShadow
	shadow.mapSize.width={2048}
	shadow.mapSize.height={2048}
	shadow.camera.left={-20}
	shadow.camera.right={20}
	shadow.camera.top={20}
	shadow.camera.bottom={-20}
/>

<!-- Edge-defining lights from camera side -->
<!-- Top highlight (from camera) -->
<T.PointLight position={[12, 8, -5]} intensity={2} color="#ffffff" distance={20} />

<!-- Front face edge highlights (directly on short side) -->
<T.PointLight position={[8, 3, 0]} intensity={2} color="#e8f4f8" distance={10} />
<T.PointLight position={[8, 0, 0]} intensity={1.5} color="#ffffff" distance={10} />
<T.PointLight position={[8, -2, 0]} intensity={1.2} color="#b3e0f2" distance={10} />

<!-- Corner highlights for glass edges -->
<T.PointLight position={[6, 3, 3]} intensity={1.5} color="#e8f4f8" distance={12} />
<T.PointLight position={[6, 3, -3]} intensity={1.5} color="#e8f4f8" distance={12} />

<!-- Camera-facing rim lights -->
<T.PointLight position={[20, 2, 0]} intensity={0.8} color="#b3e0f2" distance={15} />
<T.PointLight position={[10, -1, 0]} intensity={0.5} color="#b3e0f2" distance={12} />

<!-- Fill light from camera direction -->
<T.PointLight position={[18, 0, 0]} intensity={0.6} color="#c8e8f0" distance={15} />

<!-- Strong spotlight directly on front face -->
<T.SpotLight
	position={[10, 0, 0]}
	target.position={[0, 0, 0]}
	intensity={2}
	angle={0.5}
	penumbra={0.3}
	color="#ffffff"
	distance={15}
/>
