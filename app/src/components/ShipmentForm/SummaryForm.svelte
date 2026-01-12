<script lang="ts">
	import { reporter } from '@felte/reporter-svelte';
	import { createForm } from 'felte';
	import SummaryWrapper from '../SummaryWrapper.svelte';
	import type { CreateShipmentFormInterface } from './interfaces';

	export let onSubmit;
	export let onBack;
	export let showModal = true;
	export let initialValues: CreateShipmentFormInterface;

	const { form, data } = createForm({
		extend: [reporter],
		onSubmit
	});
</script>

<div class="text-sm text-neutral-600">
	<h2
		class="font-bold text-xl bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent"
	>
		Summary
	</h2>
	<p class="text-neutral-600 text-sm max-w-sm mt-2">Confirm creation of shipment.</p>

	<form use:form class="mt-8">
		<SummaryWrapper shipment={initialValues} />

		<div class="flex justify-center gap-4 mt-10">
			<button
				type="button"
				class="px-8 py-3 rounded-full border-2 border-gray-100 text-gray-400 text-xs font-black uppercase tracking-[0.2em] hover:bg-gray-50 transition-all"
				on:click={() => onBack($data)}
			>
				Prev
			</button>
			<button
				type="submit"
				class="px-10 py-3 rounded-full bg-gradient-to-r from-primary to-secondary text-white text-xs font-black uppercase tracking-[0.2em] shadow-lg shadow-primary/20 hover:scale-[1.02] active:scale-95 transition-all"
			>
				Finalize
			</button>
		</div>
	</form>
</div>
