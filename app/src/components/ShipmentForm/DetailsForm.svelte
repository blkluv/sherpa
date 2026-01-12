<script lang="ts">
	import { reporter, ValidationMessage } from '@felte/reporter-svelte';
	import { validator } from '@felte/validator-yup';
	import { createForm } from 'felte';
	import * as yup from 'yup';
	import Details from './Details.svelte';
	import type { DetailsFormInterface } from './interfaces';
	import { detailsFormSchema as schema } from './schemas';

	export let initialValues: DetailsFormInterface;
	export let onSubmit;
	export let onBack;

	export let showModal = true;

	const { form, data } = createForm<yup.InferType<typeof schema>>({
		extend: [reporter, validator({ schema, castValues: true })],
		onSubmit,
		initialValues
	});
</script>

<div>
	<h2
		class="font-bold text-xl bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent"
	>
		Details
	</h2>
	<p class="text-neutral-600 text-sm max-w-sm mt-2">Enter details of your shipment.</p>

	<form use:form class="mt-8">
		<Details />

		{#each ['count', 'access', 'priority', 'fragility'] as name}
			<ValidationMessage for={name} let:messages={message}>
				{#if message}
					<div class="bg-red-200 border-l-4 mt-3 border-red-400 text-orange-700 p-2" role="alert">
						<p class="font-bold">Invalid {name}</p>
						<p>{message || ''}</p>
					</div>
				{/if}
			</ValidationMessage>
		{/each}

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
				Next
			</button>
		</div>
	</form>
</div>
