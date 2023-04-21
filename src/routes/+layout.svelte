<script lang="ts">
	// The ordering of these imports is critical to your app working properly
	import "@skeletonlabs/skeleton/themes/theme-seafoam.css"
	// If you have source.organizeImports set to true in VSCode, then it will auto change this ordering
	import "@skeletonlabs/skeleton/styles/all.css"
	// Most of your app wide CSS should be put in this file
	import "../app.postcss"

	import { AppShell, AppBar, LightSwitch } from "@skeletonlabs/skeleton"
	import { onMount } from "svelte"

	onMount(() => console.log("Layout mounted"))
</script>

<!-- App Shell -->
<AppShell>
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar slotDefault="place-self-center">
			<svelte:fragment slot="lead">
				<strong class="text-xl uppercase">
					<a href="/">Transcendance</a>
				</strong>
			</svelte:fragment>
			<svelte:fragment slot="trail">
				{#if true}
					<button class="btn variant-filled-secondary text-xs font-semibold">
						Log out
					</button>
					<div class="card">
						{"John Doe"}
					</div>
				{/if}
				<LightSwitch />
			</svelte:fragment>
		</AppBar>
	</svelte:fragment>
	<!-- Page Route Content -->
	<slot />
</AppShell>
