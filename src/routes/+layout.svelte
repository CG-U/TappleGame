<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<div class="bg-base box-border h-screen w-screen text-base-content">
	{@render children()}
</div>
