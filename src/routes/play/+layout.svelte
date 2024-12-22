<script lang="ts">
	let { children } = $props();
</script>

<div class="flex h-full flex-col">
	<a href="/" aria-label="go back to main menu">
		<!-- chevron_left -->
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="1.5"
			stroke="currentColor"
			class="m-4 size-6"
		>
			<path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5 8.25 12l7.5-7.5" />
		</svg></a
	>

	<div class="flex-1">
		{@render children()}
	</div>
</div>
