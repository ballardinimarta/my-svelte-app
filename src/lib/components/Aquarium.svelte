<script lang="ts">
	import { onMount } from 'svelte';
	import { T } from '@threlte/core';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js';

	export let position: [number, number, number] = [0, 0, 0];
	export let rotation: [number, number, number] = [0, 0, 0];
	export let scale: number = 1;

	let groupRef: THREE.Group;
	let cubeRef: THREE.Mesh;
	let loaded = false;
	let gltfScene: THREE.Object3D;

	// Apply stencil mask to all contents
	$: if (groupRef) {
		groupRef.traverse((child: any) => {
			if (child.material) {
				// Apply stencil reading to match the glass stencil
				child.material.stencilWrite = false;
				child.material.stencilFunc = THREE.EqualStencilFunc;
				child.material.stencilRef = 1;
			}
		});
	}

	onMount(() => {
		const dracoLoader = new DRACOLoader();
		dracoLoader.setDecoderPath('https://www.gstatic.com/draco/versioned/decoders/1.5.6/');

		const loader = new GLTFLoader();
		loader.setDRACOLoader(dracoLoader);

		loader.load('/shapes-transformed.glb', (gltf) => {
			if (gltf.scene) {
				gltfScene = gltf.scene;
				gltfScene.scale.set(0.61 * 6, 0.8 * 6, 1 * 6);

				// Enable stencil masking for glass
				gltfScene.traverse((child) => {
					if (child instanceof THREE.Mesh) {
						child.renderOrder = -1;
					}
				});

				const materials = gltf.parser?.json?.materials;

				// Load textures if they exist
				if (gltf.parser?.json?.textures?.length > 0) {
					gltf.parser.getDependencies('texture').then((loadedTextures: THREE.Texture[]) => {
						gltfScene.traverse((child) => {
							if (child instanceof THREE.Mesh && child.material) {
								const mat = child.material as THREE.MeshStandardMaterial;
								const materialName = mat.name;

								// Find the corresponding material definition
								const matDef = materials?.find((m: any) => m.name === materialName);

								// Create glass material
								const glassMaterial = new THREE.MeshPhysicalMaterial();

								// Check if there's a texture in the extension
								if (matDef?.extensions?.KHR_materials_pbrSpecularGlossiness) {
									const ext = matDef.extensions.KHR_materials_pbrSpecularGlossiness;

									// Apply diffuse texture if exists
									if (ext.diffuseTexture && ext.diffuseTexture.index < loadedTextures.length) {
										glassMaterial.map = loadedTextures[ext.diffuseTexture.index];
										glassMaterial.map.needsUpdate = true;
									}
								} else {
									// Copy existing texture if available
									if (mat.map) glassMaterial.map = mat.map;
								}

								// Copy color
								if (mat.color) glassMaterial.color.copy(mat.color);

								// Optimized glass properties for maximum transmission and clarity
								glassMaterial.side = THREE.DoubleSide;
								glassMaterial.transparent = true;
								glassMaterial.opacity = 1;
								glassMaterial.transmission = 1;
								glassMaterial.thickness = 1;
								glassMaterial.roughness = 0;
								glassMaterial.metalness = 0;
								glassMaterial.ior = 1.5;
								glassMaterial.envMapIntensity = 2;
								glassMaterial.attenuationDistance = 10;
								glassMaterial.attenuationColor = new THREE.Color('#ffffff');

								// Iridescence for glass rainbow effect (view-dependent)
								glassMaterial.iridescence = 1;
								glassMaterial.iridescenceIOR = 1.5;
								glassMaterial.iridescenceThicknessRange = [200, 1200];

								// Enhanced clearcoat for strong front-facing highlights
								glassMaterial.clearcoat = 1;
								glassMaterial.clearcoatRoughness = 0.05;
								glassMaterial.reflectivity = 0.9;

								// Stencil masking (matching React's useMask)
								glassMaterial.depthWrite = false;
								glassMaterial.stencilWrite = true;
								glassMaterial.stencilFunc = THREE.AlwaysStencilFunc;
								glassMaterial.stencilRef = 1;
								glassMaterial.stencilZPass = THREE.ReplaceStencilOp;

								child.material = glassMaterial;
								child.castShadow = true;
								child.receiveShadow = true;
							}
						});
					});
				} else {
					// No textures, just apply glass material
					gltfScene.traverse((child) => {
						if (child instanceof THREE.Mesh && child.material) {
							const glassMaterial = new THREE.MeshPhysicalMaterial();

							if (child.material.color) glassMaterial.color.copy(child.material.color);

							glassMaterial.side = THREE.DoubleSide;
							glassMaterial.transparent = true;
							glassMaterial.opacity = 1;
							glassMaterial.transmission = 1;
							glassMaterial.thickness = 1;
							glassMaterial.roughness = 0;
							glassMaterial.metalness = 0;
							glassMaterial.ior = 1.5;
							glassMaterial.envMapIntensity = 2;
							glassMaterial.attenuationDistance = 10;
							glassMaterial.attenuationColor = new THREE.Color('#ffffff');

							// Iridescence for glass rainbow effect (view-dependent)
							glassMaterial.iridescence = 1;
							glassMaterial.iridescenceIOR = 1.5;
							glassMaterial.iridescenceThicknessRange = [200, 1200];

							// Enhanced clearcoat for strong front-facing highlights
							glassMaterial.clearcoat = 1;
							glassMaterial.clearcoatRoughness = 0.05;
							glassMaterial.reflectivity = 0.9;

							// Stencil masking
							glassMaterial.depthWrite = false;
							glassMaterial.stencilWrite = true;
							glassMaterial.stencilFunc = THREE.AlwaysStencilFunc;
							glassMaterial.stencilRef = 1;
							glassMaterial.stencilZPass = THREE.ReplaceStencilOp;

							child.material = glassMaterial;
							child.castShadow = true;
							child.receiveShadow = true;
						}
					});
				}
			}
			loaded = true;
		});
	});
</script>

<T.Group {position} rotation.x={rotation[0]} rotation.y={rotation[1]} rotation.z={rotation[2]}>
	<!-- Container for glass - ONLY the glass is scaled -->
	<T.Group {scale}>
		<!-- Glass aquarium (rendered outside) -->
		{#if loaded && gltfScene}
			<T is={gltfScene} />
		{:else}
			<!-- Fallback glass box -->
			<T.Mesh bind:ref={cubeRef} castShadow receiveShadow scale={[3.66, 4.8, 6]}>
				<T.BoxGeometry args={[1, 1, 1]} />
				<T.MeshPhysicalMaterial
					side={THREE.DoubleSide}
					transparent
					opacity={1}
					transmission={1}
					thickness={1}
					roughness={0}
					metalness={0}
					ior={1.5}
					envMapIntensity={2}
					iridescence={1}
					iridescenceIOR={1.5}
					iridescenceThicknessRange={[200, 1200]}
					clearcoat={1}
					clearcoatRoughness={0.05}
					reflectivity={0.9}
					depthWrite={false}
					stencilWrite={true}
					stencilFunc={THREE.AlwaysStencilFunc}
					stencilRef={1}
					stencilZPass={THREE.ReplaceStencilOp}
				/>
			</T.Mesh>
		{/if}
	</T.Group>

	<!-- Contents group (turtle and spheres) - NOT scaled, only stencil masked -->
	<T.Group bind:ref={groupRef}>
		<slot />
	</T.Group>
</T.Group>
