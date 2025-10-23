<script lang="ts">
	import ConfettiBackground from '$lib/components/ConfettiBackground.svelte';
	let mobileMenuOpen = false;
</script>

<div class="min-h-screen bg-gradient-to-b from-slate-50 to-white">
	<!-- Navigation -->
	<nav class="fixed z-50 w-full border-b border-slate-700 bg-slate-900/90 backdrop-blur-md">
		<div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
			<div class="flex h-16 items-center justify-between">
				<div class="flex-shrink-0 text-xl font-bold text-white">YourBrand</div>

				<!-- Desktop Navigation -->
				<div class="hidden space-x-8 md:flex">
					<a href="#features" class="text-slate-300 transition-colors hover:text-white">Features</a>
					<a href="#about" class="text-slate-300 transition-colors hover:text-white">About</a>
					<a href="#pricing" class="text-slate-300 transition-colors hover:text-white">Pricing</a>
					<a href="#contact" class="text-slate-300 transition-colors hover:text-white">Contact</a>
				</div>

				<div class="hidden space-x-4 md:flex">
					<button class="px-4 py-2 text-slate-300 transition-colors hover:text-white">
						Sign In
					</button>
					<button
						class="rounded-lg bg-white px-4 py-2 text-slate-900 transition-colors hover:bg-slate-100"
					>
						Get Started
					</button>
				</div>

				<!-- Mobile menu button -->
				<button
					class="p-2 text-white md:hidden"
					on:click={() => (mobileMenuOpen = !mobileMenuOpen)}
					aria-label="Toggle menu"
				>
					<svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M4 6h16M4 12h16M4 18h16"
						/>
					</svg>
				</button>
			</div>
		</div>

		<!-- Mobile Menu -->
		{#if mobileMenuOpen}
			<div class="border-t border-slate-700 bg-slate-900 md:hidden">
				<div class="space-y-3 px-4 py-3">
					<a href="#features" class="block text-slate-300 hover:text-white">Features</a>
					<a href="#about" class="block text-slate-300 hover:text-white">About</a>
					<a href="#pricing" class="block text-slate-300 hover:text-white">Pricing</a>
					<a href="#contact" class="block text-slate-300 hover:text-white">Contact</a>
					<div class="space-y-2 pt-3">
						<button class="w-full rounded-lg border border-slate-600 px-4 py-2 text-slate-300">
							Sign In
						</button>
						<button class="w-full rounded-lg bg-white px-4 py-2 text-slate-900">
							Get Started
						</button>
					</div>
				</div>
			</div>
		{/if}
	</nav>

	<!-- Hero Section -->
	<section class="pb-50 relative px-4 pt-32 sm:px-6 lg:px-8">
		<ConfettiBackground dash={0.9} count={100} radius={50} />
		<div class="relative z-10 mx-auto max-w-7xl">
			<div class="grid items-center gap-12 lg:grid-cols-2">
				<div class="text-center lg:text-left">
					<h1 class="mb-6 text-5xl font-bold text-white md:text-6xl lg:text-7xl">
						This is
						<span
							class="bg-gradient-to-r from-blue-600 to-purple-600 bg-clip-text text-transparent"
						>
							Spagetti Code
						</span>
					</h1>
					<p class="mx-auto mb-10 max-w-3xl text-xl text-white md:text-2xl lg:mx-0">
						Create beautiful, fast, and modern web applications with ease. Start your journey today
						and bring your ideas to life.
					</p>
					<div class="flex flex-col justify-center gap-4 sm:flex-row lg:justify-start">
						<button
							class="rounded-lg bg-slate-900 px-8 py-4 text-lg font-semibold text-white shadow-lg transition-colors hover:bg-slate-800"
						>
							Get Started Free
						</button>
						<button
							class="rounded-lg border-2 border-white px-8 py-4 text-lg font-semibold text-white transition-colors hover:bg-slate-900"
						>
							View Demo
						</button>
					</div>
				</div>
			</div>
		</div>
	</section>

	<!-- Features Section -->
	<section id="features" class="bg-white px-4 py-20 sm:px-6 lg:px-8">
		<div class="mx-auto max-w-7xl">
			<h2 class="mb-16 text-center text-4xl font-bold text-slate-900">Why Choose Us</h2>
			<div class="grid gap-8 md:grid-cols-3">
				<div class="rounded-xl border border-slate-200 p-6 transition-shadow hover:shadow-lg">
					<div class="mb-4 flex h-12 w-12 items-center justify-center rounded-lg bg-blue-100">
						<svg
							class="h-6 w-6 text-blue-600"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M13 10V3L4 14h7v7l9-11h-7z"
							/>
						</svg>
					</div>
					<h3 class="mb-2 text-xl font-semibold text-slate-900">Lightning Fast</h3>
					<p class="text-slate-600">Optimized performance for the best user experience.</p>
				</div>

				<div class="rounded-xl border border-slate-200 p-6 transition-shadow hover:shadow-lg">
					<div class="mb-4 flex h-12 w-12 items-center justify-center rounded-lg bg-purple-100">
						<svg
							class="h-6 w-6 text-purple-600"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"
							/>
						</svg>
					</div>
					<h3 class="mb-2 text-xl font-semibold text-slate-900">Secure</h3>
					<p class="text-slate-600">Built with security best practices from the ground up.</p>
				</div>

				<div class="rounded-xl border border-slate-200 p-6 transition-shadow hover:shadow-lg">
					<div class="mb-4 flex h-12 w-12 items-center justify-center rounded-lg bg-green-100">
						<svg
							class="h-6 w-6 text-green-600"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z"
							/>
						</svg>
					</div>
					<h3 class="mb-2 text-xl font-semibold text-slate-900">Scalable</h3>
					<p class="text-slate-600">Grow from prototype to production seamlessly.</p>
				</div>
			</div>
		</div>
	</section>

	<!-- CTA Section -->
	<section class="px-4 py-20 sm:px-6 lg:px-8">
		<div
			class="mx-auto max-w-4xl rounded-2xl bg-gradient-to-r from-blue-600 to-purple-600 p-12 text-center text-white"
		>
			<h2 class="mb-4 text-3xl font-bold md:text-4xl">Ready to Get Started?</h2>
			<p class="mb-8 text-lg opacity-90">Join thousands of developers building amazing things.</p>
			<button
				class="rounded-lg bg-white px-8 py-4 text-lg font-semibold text-slate-900 shadow-lg transition-colors hover:bg-slate-100"
			>
				Start Free Trial
			</button>
		</div>
	</section>

	<!-- Footer -->
	<footer class="bg-slate-900 px-4 py-12 text-white sm:px-6 lg:px-8">
		<div class="mx-auto max-w-7xl text-center">
			<p class="text-slate-400">© 2025 YourBrand. All rights reserved.</p>
		</div>
	</footer>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
	}
</style>
