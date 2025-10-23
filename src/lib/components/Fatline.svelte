<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import { MeshLineGeometry, MeshLineMaterial } from 'meshline';
	import type { Mesh } from 'three';
	import * as THREE from 'three';
	import { onMount } from 'svelte';

	let { curve, width, color, speed, dash } = $props<{
		curve: number[];
		width: number;
		color: any;
		speed: number;
		dash: number;
	}>();

	let meshRef: Mesh | undefined = $state();
	let geometry: MeshLineGeometry | undefined = $state();
	let material: MeshLineMaterial | undefined = $state();

	onMount(() => {
		geometry = new MeshLineGeometry();

		// Convert flat array to Vector3 array
		const points: THREE.Vector3[] = [];
		for (let i = 0; i < curve.length; i += 3) {
			points.push(new THREE.Vector3(curve[i], curve[i + 1], curve[i + 2]));
		}

		geometry.setPoints(points);

		// Handle both array colors (RGB) and hex colors
		let colorValue: THREE.Color;
		if (Array.isArray(color)) {
			// For HDR colors (values > 1.0), set r,g,b directly
			colorValue = new THREE.Color();
			colorValue.r = color[0];
			colorValue.g = color[1];
			colorValue.b = color[2];
		} else {
			colorValue = new THREE.Color(color);
		}

		material = new MeshLineMaterial({
			lineWidth: width,
			color: colorValue,
			dashArray: 0.25,
			dashRatio: dash
		} as any);

		material.transparent = true;
		material.depthWrite = false;
		material.toneMapped = false;
	});

	useTask((delta) => {
		if (material) {
			material.dashOffset -= (delta * speed) / 30;
		}
	});
</script>

{#if geometry && material}
	<T.Mesh {geometry} {material} bind:ref={meshRef} />
{/if}
