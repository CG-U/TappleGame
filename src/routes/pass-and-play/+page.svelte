<script lang="ts">
	import { fade } from 'svelte/transition';
	import { onDestroy } from 'svelte';

	let usedLetters: string[] = [];
	export let data;

	let { letters, category } = data;
	function shuffle(array: string[]) {
		let currentIndex = array.length;

		// While there remain elements to shuffle...
		while (currentIndex != 0) {
			// Pick a remaining element...
			const randomIndex = Math.floor(Math.random() * currentIndex);
			currentIndex--;

			// And swap it with the current element.
			[array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
		}

		return array;
	}

	let difficulty = 'Select Difficulty';
	let showCategory = false;
	let reset = false;
	let openSettings = false;

	let timer = 8;
	let seconds: number = timer;

	function resetClock() {
		seconds = timer;
	}

	let interval: any;
	const startCountdown = () => {
		interval = setInterval(() => {
			if (seconds > 0) {
				seconds -= 1;
			} else {
				clearInterval(interval); // Stop the interval when it reaches 0
			}
		}, 1000);
	};

	$: if (showCategory) {
		startCountdown();
	}

	$: if (seconds === 0) {
		reset = true;
	}

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<div
	class="mx-auto flex h-full w-full max-w-3xl flex-col items-center justify-center space-y-20 px-8"
>
	<div class="w-full rounded-lg p-2">
		<h1 class="text-orange-600">tapple</h1>

		{#if !showCategory}
			<div class="flex flex-col items-center space-y-2">
				<button
					onclick={() => {
						showCategory = true;
						letters = shuffle(letters);
						reset = false;
						resetClock();
					}}
					class="btn btn-primary w-full">Start Game</button
				>

				<div class="flex w-full space-x-1">
					<label class="flex items-center font-bold">
						Timer
						<input
							class="w-full rounded-lg px-2 py-3"
							type="number"
							name="timer"
							bind:value={timer}
						/>
					</label>
					<div class="dropdown dropdown-bottom dropdown-hover flex-1">
						<div tabindex="-1" class="btn w-full">{difficulty}</div>
						<ul
							tabindex="-1"
							class="menu dropdown-content z-[1] w-full rounded-box bg-base-100 p-2 shadow"
						>
							<li class="transition-all duration-300 hover:text-lg">
								<button
									onclick={() => {
										difficulty = 'Normal';
									}}
									class="flex justify-center"
								>
									Normal
								</button>
							</li>
							<li class="transition-all duration-300 hover:text-lg">
								<button
									onclick={() => {
										difficulty = 'Dyslexic';
									}}
									class="flex justify-center"
									>Dyslexic
								</button>
							</li>
							<li class="transition-all duration-300 hover:text-lg">
								<button
									onclick={() => {
										difficulty = 'I Cant Read';
									}}
									class="flex justify-center"
								>
									I Cant Read
								</button>
							</li>
						</ul>
					</div>
				</div>
			</div>
		{:else}
			<div class="flex justify-between">
				<div class="flex items-center text-8xl">
					<p in:fade>{category}</p>
				</div>
				<p class="text-8xl">{seconds}</p>
			</div>
		{/if}
	</div>

	<div class={`${showCategory ? 'grid' : 'hidden'} grid-cols-12 gap-3`} in:fade>
		{#each letters as letter}
			<button
				class={`${!usedLetters.includes(letter) ? 'btn' : 'btn btn-disabled'}`}
				onclick={() => {
					if (seconds !== 0) {
						usedLetters = [...usedLetters, letter];
						resetClock();
					}
				}}
				disabled={!showCategory}>{letter}</button
			>
		{/each}
	</div>

	{#if reset}
		<button
			class="btn btn-error"
			onclick={() => {
				usedLetters = [];
				showCategory = false;
				reset = false;
			}}>RESET</button
		>
	{/if}
</div>
