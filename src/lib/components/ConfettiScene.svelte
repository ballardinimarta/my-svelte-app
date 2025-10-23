<script lang="ts">
	import { T, useTask } from '@threlte/core';
	import * as THREE from 'three';
	import { MeshLineGeometry, MeshLineMaterial } from 'meshline';
	import Fatline from './Fatline.svelte';
	import CameraRig from './CameraRig.svelte';
	import { EffectComposer, RenderPass } from 'postprocessing';
	import { useThrelte } from '@threlte/core';
	import { onMount } from 'svelte';
	import { BloomEffect, EffectPass } from 'postprocessing';

	let { dash = 0.9, count = 50, radius = 50 } = $props();

	const colors = [[10, 0.5, 2], [1, 2, 10], '#A2CCB6', '#FCEEB5', '#EE786E', '#e0feff'];

	interface LineData {
		color: any;
		width: number;
		speed: number;
		curve: number[];
	}

	const rand = THREE.MathUtils.randFloatSpread;
	const lines = Array.from({ length: count }, () => {
		const pos = new THREE.Vector3(rand(radius), rand(radius), rand(radius));
		const points = Array.from({ length: 140 }, () =>
			pos.add(new THREE.Vector3(rand(radius), rand(radius), rand(radius))).clone()
		);
		const curve = new THREE.CatmullRomCurve3(points).getPoints(4200);
		return {
			color: colors[Math.floor(colors.length * Math.random())],
			width: Math.max(radius / 100, (radius / 50) * Math.random()),
			speed: Math.max(0.1, 1 * Math.random()),
			curve: curve.flatMap((point) => point.toArray())
		};
	});

	const { scene, camera, renderer, size, autoRender } = useThrelte();

	onMount(() => {
		autoRender.set(false);

		const composer = new EffectComposer(renderer);
		composer.addPass(new RenderPass(scene, camera.current));

		const bloomEffect = new BloomEffect({
			mipmapBlur: true,
			luminanceThreshold: 0.7,
			intensity: 2,
			radius: 0.6
		});

		const effectPass = new EffectPass(camera.current, bloomEffect);
		composer.addPass(effectPass);

		const unsubscribeSize = size.subscribe((s) => {
			composer.setSize(s.width, s.height);
		});

		useTask(() => {
			composer.render();
		});

		return () => {
			unsubscribeSize();
			composer.dispose();
			autoRender.set(true);
		};
	});
</script>

<T.PerspectiveCamera makeDefault position={[0, 0, 5]} fov={90} />

{#each lines as line, i (i)}
	<Fatline {dash} {...line} />
{/each}

<CameraRig />
