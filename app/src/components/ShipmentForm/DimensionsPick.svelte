<script lang="ts">
	import clsx from 'clsx';
	import DecimalInput from '../Inputs/DecimalInput.svelte';

	export let metrics: {
		weight: 'kg' | 'lb';
		distance: 'm' | 'ft';
	} = {
		distance: 'm',
		weight: 'kg'
	};

	export let weight: number | undefined = undefined;
	export let height: number | undefined = undefined;
	export let width: number | undefined = undefined;
	export let depth: number | undefined = undefined;
	export let volume: number | undefined = undefined;

	export let isMetricTon: boolean = false;
</script>

<div class="space-y-8 px-1">
	<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
		<div class="flex flex-col items-center space-y-3">
			<span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Type</span>
			<button
				type="button"
				on:click={() => (isMetricTon = !isMetricTon)}
				class={clsx(
					'relative w-full py-2 px-4 rounded-xl text-xs font-bold border transition-all duration-200',
					isMetricTon
						? 'bg-primary/5 border-primary text-primary'
						: 'bg-gray-50 border-gray-100 text-gray-500'
				)}
			>
				{isMetricTon ? 'Metric Tons' : 'Standard'}
			</button>
		</div>

		<div class="flex flex-col items-center space-y-3">
			<span class="text-[10px] font-black text-gray-400 uppercase tracking-widest">Weight Unit</span
			>
			<div class="flex bg-gray-50 p-1 rounded-xl w-full border border-gray-100">
				{#each ['kg', 'lb'] as unit}
					<button
						type="button"
						class={clsx(
							'flex-1 py-1.5 rounded-lg text-xs font-bold transition-all',
							metrics.weight === unit ? 'bg-white shadow-sm text-primary' : 'text-gray-400'
						)}
						on:click={() => (metrics.weight = unit)}
					>
						{unit.toUpperCase()}
					</button>
				{/each}
			</div>
		</div>

		<div class="flex flex-col items-center space-y-3">
			<span class="text-[10px] font-black text-gray-400 uppercase tracking-widest"
				>Distance Unit</span
			>
			<div class="flex bg-gray-50 p-1 rounded-xl w-full border border-gray-100">
				{#each ['m', 'ft'] as unit}
					<button
						type="button"
						class={clsx(
							'flex-1 py-1.5 rounded-lg text-xs font-bold transition-all',
							metrics.distance === unit ? 'bg-white shadow-sm text-primary' : 'text-gray-400'
						)}
						on:click={() => (metrics.distance = unit)}
					>
						{unit.toUpperCase()}
					</button>
				{/each}
			</div>
		</div>
	</div>

	<div class="space-y-4">
		<h4 class="text-[10px] font-black text-gray-400 uppercase tracking-widest">
			Dimensions & Weight
		</h4>

		<div class="bg-gray-50/50 rounded-2xl p-4 md:p-6 border border-gray-100 shadow-inner">
			{#if isMetricTon}
				<div class="grid grid-cols-2 gap-4 md:gap-8">
					<div class="space-y-2">
						<!-- svelte-ignore a11y-label-has-associated-control -->
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase"
							>Weight ({metrics.weight})</label
						>
						<DecimalInput bind:value={weight} name="weight" placeholder="0.00" required />
					</div>
					<div class="space-y-2">
						<!-- svelte-ignore a11y-label-has-associated-control -->
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase"
							>Volume ({metrics.distance}³)</label
						>
						<DecimalInput bind:value={volume} name="volume" placeholder="0.00" required />
					</div>
				</div>
			{:else}
				<div class="grid grid-cols-2 md:grid-cols-4 gap-4 md:gap-6">
					<div class="space-y-2">
						<!-- svelte-ignore a11y-label-has-associated-control -->
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase text-center block"
							>Width</label
						>
						<DecimalInput bind:value={width} name="width" placeholder="0.0" required />
					</div>
					<div class="space-y-2">
						<!-- svelte-ignore a11y-label-has-associated-control -->
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase text-center block"
							>Height</label
						>
						<DecimalInput bind:value={height} name="height" placeholder="0.0" required />
					</div>
					<div class="space-y-2">
						<!-- svelte-ignore a11y-label-has-associated-control -->
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase text-center block"
							>Depth</label
						>
						<DecimalInput bind:value={depth} name="depth" placeholder="0.0" required />
					</div>
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<div class="space-y-2">
						<label class="text-[10px] font-bold text-gray-400 ml-1 uppercase text-center block"
							>Weight</label
						>
						<DecimalInput bind:value={weight} name="weight" placeholder="0.0" required />
					</div>
				</div>
			{/if}
		</div>
	</div>
</div>
