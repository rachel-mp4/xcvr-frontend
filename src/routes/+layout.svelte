<script lang="ts">
	import { page } from "$app/stores";
	import type { LayoutProps } from "./$types";
	import Spectrum from "$lib/components/Spectrum.svelte";
	let { data, children }: LayoutProps = $props();
</script>

<div id="content">
	<aside id="left-sidebar">
		<nav>
			<a class="block-link" href="/">
				{#if $page.url.pathname === "/"}
					& now you're home
				{:else}
					go home
				{/if}
			</a>
			{#if data.id}
				<a class="block-link" href="/p/{data.id.handle}">
					i know who you are
				</a>
			{:else}
				<a class="block-link" href="/login"> log in with atproto </a>
			{/if}
		</nav>

		<a class="block-link" href="/c/create"> create a channel</a>
		<div class="beep">here's what's been happening recently</div>
		<Spectrum channels={data.channels}></Spectrum>
	</aside>
	{@render children()}
</div>

<style>
	#left-sidebar {
		position: sticky;
		top: 0;
	}
	#content {
		display: grid;
		position: relative;
		grid-template-columns: 20rem 1fr 20rem;
		height: 100vh;
	}
	.beep {
		margin-top: 16rem;
	}
	nav {
		top: 0rem;
		line-height: 1;
		border-bottom: 0.25rem solid var(--fg);
	}
</style>
