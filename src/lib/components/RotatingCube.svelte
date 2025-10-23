<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { OrbitControls } from '@threlte/extras';
	import * as THREE from 'three';

	let cubeRef: THREE.Mesh;
	let octopusGroupRef: THREE.Group;
	let elapsedTime = 0;

	// Auto-rotate the cube
	useTask((delta: number) => {
		elapsedTime += delta;

		if (cubeRef) {
			cubeRef.rotation.x += delta * 0.2;
			cubeRef.rotation.y += delta * 0.3;
		}

		// Animate octopus
		if (octopusGroupRef) {
			octopusGroupRef.rotation.y = Math.sin(elapsedTime * 0.5) * 0.3;
			octopusGroupRef.position.y = Math.sin(elapsedTime) * 0.2;
		}
	});
</script>

<T.PerspectiveCamera makeDefault position={[0, 0, 8]}>
	<OrbitControls
		enableZoom={true}
		enablePan={false}
		minDistance={5}
		maxDistance={15}
		autoRotate={false}
	/>
</T.PerspectiveCamera>

<!-- Glass Cube -->
<T.Mesh bind:ref={cubeRef} castShadow receiveShadow>
	<T.BoxGeometry args={[3, 3, 3]} />
	<T.MeshPhysicalMaterial
		transparent
		opacity={0.2}
		roughness={0}
		metalness={0}
		transmission={0.9}
		thickness={0.5}
		envMapIntensity={1.5}
		clearcoat={1}
		clearcoatRoughness={0}
	/>
</T.Mesh>

<!-- Octopus inside -->
<T.Group bind:ref={octopusGroupRef}>
	<!-- Octopus head -->
	<T.Mesh position={[0, 0.3, 0]}>
		<T.SphereGeometry args={[0.6, 32, 32]} />
		<T.MeshStandardMaterial color="#ff6b9d" roughness={0.3} metalness={0.2} />
	</T.Mesh>

	<!-- Eyes -->
	<T.Mesh position={[-0.2, 0.4, 0.5]}>
		<T.SphereGeometry args={[0.1, 16, 16]} />
		<T.MeshStandardMaterial color="#ffffff" />
	</T.Mesh>
	<T.Mesh position={[0.2, 0.4, 0.5]}>
		<T.SphereGeometry args={[0.1, 16, 16]} />
		<T.MeshStandardMaterial color="#ffffff" />
	</T.Mesh>

	<!-- Pupils -->
	<T.Mesh position={[-0.2, 0.4, 0.6]}>
		<T.SphereGeometry args={[0.05, 16, 16]} />
		<T.MeshStandardMaterial color="#000000" />
	</T.Mesh>
	<T.Mesh position={[0.2, 0.4, 0.6]}>
		<T.SphereGeometry args={[0.05, 16, 16]} />
		<T.MeshStandardMaterial color="#000000" />
	</T.Mesh>

	<!-- Tentacles - 8 tentacles around the body -->
	{#each Array(8) as _, i}
		{@const angle = (i / 8) * Math.PI * 2}
		{@const x = Math.cos(angle) * 0.4}
		{@const z = Math.sin(angle) * 0.4}

		<T.Group position={[x, -0.2, z]}>
			<T.Mesh position={[0, -0.2, 0]} rotation={[0.3, 0, 0]}>
				<T.CylinderGeometry args={[0.08, 0.05, 0.4, 8]} />
				<T.MeshStandardMaterial color="#ff8bb3" roughness={0.3} metalness={0.2} />
			</T.Mesh>
			<T.Mesh position={[0, -0.5, 0]} rotation={[0.5, 0, 0]}>
				<T.CylinderGeometry args={[0.05, 0.03, 0.3, 8]} />
				<T.MeshStandardMaterial color="#ffb3d1" roughness={0.3} metalness={0.2} />
			</T.Mesh>
		</T.Group>
	{/each}
</T.Group>

<T.AmbientLight intensity={0.5} />
<T.DirectionalLight
	position={[5, 5, 5]}
	intensity={1}
	castShadow
	shadow-mapSize-width={1024}
	shadow-mapSize-height={1024}
/>
<T.PointLight position={[-5, -5, -5]} intensity={0.5} color="#9333ea" />
