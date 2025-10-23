<script lang="ts">
	import { browser } from '$app/environment';
	import { Canvas } from '@threlte/core';
	import RotatingCube from './RotatingCube.svelte';
</script>

{#if browser}
	<div class="relative h-full w-full" style="min-height: 400px; background: #c6e5db;">
		<Canvas shadows colorManagementEnabled>
			<RotatingCube />
		</Canvas>

		<div class="pointer-events-none absolute left-1/2 top-8 -translate-x-1/2 text-center">
			<p class="text-gray-600">Drag to rotate • Scroll to zoom</p>
		</div>
	</div>
{:else}
	<div
		class="flex h-full w-full items-center justify-center bg-gray-900"
		style="min-height: 400px;"
	>
		<p class="text-gray-400">Loading 3D Scene...</p>
	</div>
{/if}
