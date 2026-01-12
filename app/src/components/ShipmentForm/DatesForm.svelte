<script lang="ts">
	import { ValidationMessage, reporter } from '@felte/reporter-svelte';
	import { validator } from '@felte/validator-yup';
	import { DateInput } from 'date-picker-svelte';
	import { createForm } from 'felte';
	import * as yup from 'yup';
	import type { DatesFormInterface } from './interfaces';
	import { dateFormSchema as schema } from './schemas';

	export let initialValues: DatesFormInterface;
	export let onSubmit: any;
	export let onBack: any;
	export let showModal = true;

	const { form, data, touched } = createForm<yup.InferType<typeof schema>>({
		extend: [reporter, validator({ schema })],
		onSubmit,
		initialValues
	});

	const deadlineInitial = $data.deadline;
	const whenInitial = $data.when;

	$: {
		if (deadlineInitial !== $data.deadline) {
			$touched.deadline = true;
		}

		if (whenInitial !== $data.when) {
			$touched.when = true;
		}
	}
</script>

<div>
	<h2
		class="font-bold text-xl bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent"
	>
		Deadline
	</h2>
	<p class="text-neutral-600 text-sm max-w-sm mt-2">
		Enter the desired date and deadline date of your shipment.
	</p>

	<form use:form class="mt-8 px-4">
		<span class="flex items-center justify-between">
			<p class="text-neutral-600">Deadline of the shipment:</p>
			<div>
				<DateInput
					format="yyyy/MM/dd HH:mm"
					placeholder="2000/31/12 23:59"
					required
					timePrecision="minute"
					bind:value={$data.deadline}
				/>

				<ValidationMessage for="deadline" let:messages>
					{#if messages}
						<div class="absolute mt-1 -ml-10" role="alert">
							<p class="text-md text-red-600 font-semibold">{messages[0]}</p>
						</div>
					{/if}
				</ValidationMessage>
			</div>
		</span>

		<span class="flex items-center justify-between mt-5">
			<p class="text-neutral-600">Desired date of the shipment:</p>
			<div>
				<DateInput
					format="yyyy/MM/dd HH:mm"
					placeholder="2000/31/12 23:59"
					timePrecision="minute"
					required
					bind:value={$data.when}
				/>

				<ValidationMessage for="when" let:messages>
					{#if messages}
						<div class="absolute mt-1 -ml-10" role="alert">
							<p class="text-md text-red-600 font-semibold">{messages[0]}</p>
						</div>
					{/if}
				</ValidationMessage>
			</div>
		</span>

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
