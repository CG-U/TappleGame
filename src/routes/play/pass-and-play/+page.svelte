<script lang="ts">
	import { fade } from 'svelte/transition';
	import { onDestroy } from 'svelte';

	let usedLetters: string[] = [];
	export let data;

	let { letters, categories } = data;
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

	let introduceCategoryInterval: any;
	let introduceCategory = false;
	const startCategoryCountdown = () => {
		introduceCategoryInterval = setInterval(() => {
			introduceCategory = false;
			startCountdown();

			return clearInterval(introduceCategoryInterval);
		}, 4000);
	};

	let category: string;
	const getRandomCategory = () => {
		return categories[Math.floor(Math.random() * categories.length)];
	};
	$: if (showCategory) {
		introduceCategory = true;
		category = getRandomCategory();
		startCategoryCountdown();
	}

	$: if (seconds === 0) {
		reset = true;
	}

	onDestroy(() => {
		clearInterval(interval);
		clearInterval(introduceCategoryInterval);
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
						letters =
							difficulty === 'Normal' || difficulty === 'Select Difficulty'
								? letters.sort()
								: shuffle(letters);
						reset = false;
						resetClock();
					}}
					class="btn btn-primary w-full">Start Game</button
				>

				<div class="flex w-full space-x-1">
					<label class="flex items-center font-bold">
						Timer
						<input
							class="ml-2 w-full rounded-lg px-2 py-3"
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
		{:else if introduceCategory}
			<p>This round's category is: <span class="animate-fade-in">{category}</span></p>
		{:else}
			<div class="flex justify-between">
				<div class="flex items-center text-8xl">
					<p in:fade>{category}</p>
				</div>
				<p class="text-8xl">{seconds}</p>
				<p>{usedLetters.at(-1)}</p>
			</div>
		{/if}
	</div>

	<div class={`${showCategory ? 'grid' : 'hidden'} grid-cols-12 gap-3`} in:fade>
		{#each letters as letter, index}
			<button
				class={`${!usedLetters.includes(letter) ? 'btn' : 'animate-spin-out btn btn-disabled'} ${difficulty === 'I Cant Read' ? `animate-slow-spin` : ''}`}
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

<style>
	@keyframes spin {
		to {
			transform: rotate(360deg);
		}
	}
	.animate-slow-spin {
		animation: spin 5s linear infinite;
	}

	@keyframes spin-out {
		25% {
			transform: translateY(-10px);
		}
		to {
			opacity: 0;
			font-size: xx-small;
			visibility: hidden;
			transform: rotate(360deg) translateY(40px);
		}
	}
	.animate-spin-out {
		animation: spin-out 1s linear forwards;
	}

	@keyframes fade-in {
		from {
			opacity: 0;
		}
		50% {
			opacity: 0;
		}
		to {
			opacity: 100;
		}
	}
	.animate-fade-in {
		animation: fade-in 2s linear forwards;
	}
</style>
