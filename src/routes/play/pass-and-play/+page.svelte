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
	class="relative mx-auto flex h-full w-full max-w-3xl flex-col items-center justify-center px-8"
>
	<div
		class={`relative w-full rounded-lg p-2 ${showCategory ? 'flex justify-center' : ''} ${showCategory && !introduceCategory ? 'hidden' : ''}`}
	>
		<h1 class={`text-orange-600 ${showCategory ? 'hidden' : ''}`}>tapple</h1>

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
							min="0"
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
						</ul>
					</div>
				</div>
			</div>
		{:else if introduceCategory}
			<div class="absolute bottom-0 top-0 flex flex-col items-center space-y-2">
				<p>This round's category is:</p>
				<p class="animate-fade-in text-3xl capitalize text-orange-600">{category}</p>
			</div>
		{/if}
	</div>

	<!-- Letters -->
	<div class={`${category && showCategory && !introduceCategory ? 'circle' : 'hidden'}`} in:fade>
		<p
			class={`${seconds > 6 ? 'text-green-300' : seconds < 3 ? 'text-red-600' : 'text-yellow-600'} text-3xl`}
			hidden={seconds === 0}
		>
			{seconds}
		</p>
		{#each letters as letter, index}
			<button
				class={`${!usedLetters.includes(letter) ? 'btn' : 'animate-spin-out btn btn-disabled'} ${difficulty === 'I Cant Read' ? `animate-slow-spin` : ''}`}
				style="--index: {index}; --total: {letters.length}; --offset: 220px"
				onclick={() => {
					if (seconds !== 0) {
						usedLetters = [...usedLetters, letter];
						resetClock();
					}
				}}
				disabled={!showCategory}>{letter}</button
			>
		{/each}
		<div class="circle">
			{#if category}
				<p style="--index: {1};" class="animate-fade-in hidden whitespace-nowrap md:block">
					{category}
				</p>
				<p style="--index: {2};" class="animate-fade-in hidden whitespace-nowrap md:block">
					{category}
				</p>
				<p style="--index: {3};" class="animate-fade-in hidden whitespace-nowrap md:block">
					{category}
				</p>
				<p style="--index: {4};" class="animate-fade-in hidden whitespace-nowrap md:block">
					{category}
				</p>
			{/if}
		</div>
	</div>

	{#if reset}
		<button
			class="btn btn-error"
			onclick={() => {
				usedLetters = [];
				showCategory = false;
				reset = false;
				category = '';
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

	.circle {
		position: absolute;
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
		top: 0;
		bottom: 0;
	}

	.circle p {
		position: absolute;
		transform: rotate(calc(360deg / 4 * var(--index))) translate(270px)
			rotate(calc(-360deg / 4 * var(--index))) rotate(calc(270deg + var(--index) * 90deg));
	}

	.circle button {
		position: absolute;
		transform: rotate(calc(360deg / var(--total) * var(--index))) translate(220px)
			rotate(calc(-360deg / var(--total) * var(--index)))
			rotate(calc(270deg + var(--index) * 13.84deg));
	}

	@media (max-width: 700px) {
		.circle p {
			position: absolute;
			transform: rotate(calc(360deg / 4 * var(--index))) translate(190px)
				rotate(calc(-360deg / 4 * var(--index))) rotate(calc(270deg + var(--index) * 90deg));
		}
		.circle button {
			transform: rotate(calc(360deg / var(--total) * var(--index))) translate(150px)
				rotate(calc(-360deg / var(--total) * var(--index)))
				rotate(calc(270deg + var(--index) * 13.84deg));
		}
	}
</style>
