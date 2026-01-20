<script>
	import "./layout.css";
	import favicon from "$lib/assets/favicon.svg";
	import Navbar from "$lib/components/layout/Navbar.svelte";
	import Footer from "$lib/components/layout/Footer.svelte";
	import { page } from "$app/stores";

	let { children } = $props();

	// Hide footer on docs and playground pages
	$effect(() => {
		// This is reactive
	});
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link
		href="https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap"
		rel="stylesheet"
	/>
	<script src="https://unpkg.com/geist-font/dist/font.js"></script>
</svelte:head>

<div class="relative flex min-h-screen flex-col bg-background">
	<Navbar />
	<main class="flex-1">
		{@render children()}
	</main>
	{#if !$page.url.pathname.startsWith("/docs") && !$page.url.pathname.startsWith("/playground")}
		<Footer />
	{/if}
</div>
