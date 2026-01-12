<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { scale } from 'svelte/transition';
	const dispatch = createEventDispatcher();

	export let showModal: boolean;
	export let showCloseButton = true;

	let dialog: HTMLDialogElement;

	const onBackdropClick = () => {
		dispatch('backdropClick');
	};

	export let closeHandler = () => {
		showModal = false;
	};

	$: if (dialog && showModal) {
		dialog.showModal();
		document.body.style.overflow = 'hidden'; // Blokada przewijania tła
	} else if (dialog) {
		dialog.close();
		document.body.style.overflow = 'auto';
	}
</script>

<dialog
	bind:this={dialog}
	on:close={() => {
		showModal = false;
		document.body.style.overflow = 'auto';
	}}
	on:click|self={onBackdropClick}
	class="z-50 rounded-[2rem] overflow-y-auto p-0 bg-white shadow-2xl max-w-[90vw] md:max-w-md w-full overflow-visible border-none backdrop:bg-black/40 backdrop:backdrop-blur-sm"
>
	{#if showModal}
		<div class="relative p-6 sm:p-8" in:scale={{ duration: 300, start: 0.95 }}>
			{#if showCloseButton}
				<div class="absolute top-4 right-4 z-10">
					<button
						on:click|stopPropagation={closeHandler}
						class="p-2 rounded-full bg-gray-50 text-gray-400 hover:text-secondary hover:bg-gray-100 transition-all active:scale-90"
					>
						<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="w-6 h-6">
							<path
								fill="none"
								stroke="currentColor"
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="40"
								d="M368 368L144 144M368 144L144 368"
							/>
						</svg>
					</button>
				</div>
			{/if}

			<div class="w-full">
				<slot />
			</div>
		</div>
	{/if}
</dialog>

<style>
	/* Natywne stylowanie backdropa dla przeglądarek */
	dialog::backdrop {
		background: rgba(0, 0, 0, 0.4);
		backdrop-filter: blur(4px);
	}

	/* Usuwamy domyślne obramowanie dialogu w Chrome */
	dialog:focus-visible {
		outline: none;
	}

	/* Animacja otwierania - płynne wejście */
	dialog[open] {
		animation: show 0.3s ease-out forwards;
	}

	@keyframes show {
		from {
			opacity: 0;
			transform: translateY(10px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}
</style>
