<script lang="ts">
	import { onMount } from 'svelte';
	import { useThrelte } from '@threlte/core';
	import * as THREE from 'three';

	const { renderer } = useThrelte();

	onMount(() => {
		if (!renderer) return;

		// Configure renderer for better glass rendering
		renderer.toneMapping = THREE.ACESFilmicToneMapping;
		renderer.toneMappingExposure = 1.1;
		renderer.shadowMap.enabled = true;
		renderer.shadowMap.type = THREE.PCFSoftShadowMap;

		// Enable stencil buffer for masking
		const gl = renderer.getContext();
		gl.enable(gl.STENCIL_TEST);

		// Better output encoding for realistic colors
		renderer.outputColorSpace = THREE.SRGBColorSpace;
	});
</script>
