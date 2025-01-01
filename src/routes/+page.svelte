<script lang="ts">
	let modal: HTMLDialogElement | null = null;

	function openModal() {
		if (modal) {
			modal.showModal();
		}
	}
</script>

<div class="flex h-full w-full flex-col items-center justify-center space-y-8">
	<h1 class="text-9xl">Tapple</h1>
	<section aria-label="mode section" class="flex flex-col space-y-2">
		<a class="btn border-primary text-primary" href="/play/pass-and-play">Pass and Play</a>
		<!-- <a class="btn border-primary text-primary" href="/play/lobby-mode">Lobby Mode</a> -->

		<button class="btn" onclick={openModal}>How to Play</button>
		<dialog bind:this={modal} class="modal">
			<div class="modal-box flex flex-col space-y-4">
				<h3 class="text-lg font-bold">Game Mechanics</h3>
				<div class="space-y-4">
					<p>At the start of the game, a category will be provided to the group.</p>
					<p>
						The goal is for each player to provide a <span class="font-bold">word</span> that fits within
						the category.
					</p>
					<p>The word's first letter must be one of the remaining available letters.</p>
					<p>
						After providing a word, the player must tap on the letter matching the first letter of
						their word.
					</p>
					<p>All tasks must be completed before the timer runs out.</p>
					<p>If successful, the timer resets, and the turn passes to the next player.</p>
					<p>
						If the timer expires, the player who failed to provide a word or provided an invalid
						word faces consequences.
					</p>
				</div>

				<div class="modal-action">
					<form method="dialog">
						<button class="btn">Close</button>
					</form>
				</div>
			</div>
		</dialog>
	</section>
	<!-- <button class="rouned">Pass and play</button> -->

	<p class="font-semibold">
		created by: <span class="text-xs text-orange-600">dot</span><span
			class="text-lg font-bold text-white">Ced</span
		>
	</p>
</div>
