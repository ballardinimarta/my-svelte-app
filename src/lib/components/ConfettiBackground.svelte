<script lang="ts">
	import { Canvas } from '@threlte/core';
	import ConfettiScene from './ConfettiScene.svelte';

	let { dash = 0.9, count = 50, radius = 50 } = $props();
</script>

<div class="absolute inset-0 bg-[#101020]" style="width: 100%; height: 100%;">
	<Canvas>
		<ConfettiScene {dash} {count} {radius} />
	</Canvas>
</div>
