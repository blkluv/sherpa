<script lang="ts">
	import clsx from 'clsx';
	import type { CreateShipmentFormInterface } from './ShipmentForm/interfaces';

	export let shipment: CreateShipmentFormInterface;

	const fragilityMap = {
		1: 'Very fragile',
		2: 'Fragile',
		3: 'Normal',
		4: 'Robust',
		5: 'Very robust'
	};
	const accessMap = {
		1: 'Only car',
		2: 'Truck up to 10t',
		3: 'Truck over 10t',
		4: 'Other',
		5: 'Unknown'
	};

	const getPriority = (p: number) => {
		const maps = {
			5: { name: 'Very High', css: 'bg-red-50 text-red-700 border-red-100' },
			4: { name: 'High', css: 'bg-orange-50 text-orange-700 border-orange-100' },
			3: { name: 'Medium', css: 'bg-blue-50 text-blue-700 border-blue-100' },
			2: { name: 'Low', css: 'bg-green-50 text-green-700 border-green-100' },
			1: { name: 'Very Low', css: 'bg-gray-50 text-gray-700 border-gray-100' }
		};
		return maps[p] || { name: 'Normal', css: 'bg-gray-50 text-gray-600 border-gray-100' };
	};

	const priorityInfo = getPriority(shipment.details.priority);
</script>

<div class="flex flex-col space-y-5">
	<div
		class="relative overflow-hidden bg-white px-6 py-3 rounded-2xl border border-gray-100 shadow-sm text-center"
	>
		<div
			class="absolute top-0 right-0 -mr-4 -mt-4 w-24 h-24 bg-primary/5 rounded-full blur-2xl"
		></div>

		<span class="text-[10px] font-black text-gray-400 uppercase tracking-[0.25em] mb-1 block">
			Total Quote
		</span>
		<div class="flex items-center justify-center gap-2">
			<span
				class="text-4xl font-black bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent"
			>
				{Number(shipment.price.price).toFixed(3)}
			</span>
			<span class="text-xl font-bold text-gray-400">SOL</span>
		</div>
	</div>

	<div class="flex items-center justify-between px-1">
		<div class="flex flex-col">
			<span class="text-[9px] font-black text-gray-400 uppercase tracking-widest">Cargo Name</span>
			<p class="text-sm font-bold text-gray-800 leading-tight">{shipment.shipmentName.name}</p>
		</div>
		<div
			class={clsx(
				'px-3 py-1 rounded-full text-[10px] font-bold uppercase border',
				priorityInfo.css
			)}
		>
			{priorityInfo.name} Priority
		</div>
	</div>

	<div
		class="relative p-4 bg-gray-50/50 rounded-2xl border border-gray-100 flex items-center justify-between gap-4"
	>
		<div class="flex-1 min-w-0">
			<div class="flex items-center gap-2 mb-1">
				<div class="w-2 h-2 rounded-full border-2 border-primary bg-white"></div>
				<span class="text-[9px] font-black text-gray-400 uppercase">Pickup</span>
			</div>
			<p class="text-xs font-bold text-gray-700 truncate pl-4">{shipment.locations.sourceName}</p>
		</div>

		<div class="shrink-0 text-gray-300">
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="2.5"
				stroke="currentColor"
				class="w-5 h-5"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"
				/>
			</svg>
		</div>

		<div class="flex-1 min-w-0 text-right">
			<div class="flex items-center justify-end gap-2 mb-1">
				<span class="text-[9px] font-black text-gray-400 uppercase">Dropoff</span>
				<div class="w-2 h-2 rounded-full bg-secondary"></div>
			</div>
			<p class="text-xs font-bold text-gray-700 truncate pr-4">
				{shipment.locations.destinationName}
			</p>
		</div>
	</div>

	<div class="flex items-center justify-between p-3 bg-white border border-gray-100 rounded-xl">
		<div class="flex flex-col">
			<span class="text-[9px] font-black text-gray-400 uppercase tracking-wider"
				>Available From</span
			>
			<span class="text-xs font-bold text-gray-700"
				>{new Date(shipment.dates.when).toLocaleDateString()}</span
			>
		</div>
		<div class="w-px h-6 bg-gray-100"></div>
		<div class="flex flex-col text-right">
			<span class="text-[9px] font-black text-red-300 uppercase tracking-wider">Deadline</span>
			<span class="text-xs font-bold text-red-600"
				>{new Date(shipment.dates.deadline).toLocaleDateString()}</span
			>
		</div>
	</div>

	<div class="grid grid-cols-2 gap-x-6 gap-y-5 px-1 py-2 text-xs">
		<div class="flex items-start gap-3">
			<div class="text-lg opacity-40">📏</div>
			<div>
				<span class="text-gray-400 block mb-0.5">Dimensions</span>
				<p class="font-bold text-gray-800">
					{#if shipment.dimensions.isMetricTon}
						{shipment.dimensions.volume}<span class="text-[10px] font-medium ml-0.5"
							>{shipment.dimensions.distanceMetrics}³</span
						>
					{:else}
						{shipment.dimensions.height}×{shipment.dimensions.width}×{shipment.dimensions.depth}
						<span class="text-[10px] font-medium text-gray-400 ml-0.5"
							>{shipment.dimensions.distanceMetrics}</span
						>
					{/if}
				</p>
			</div>
		</div>

		<div class="flex items-start gap-3">
			<div class="text-lg opacity-40">⚖️</div>
			<div>
				<span class="text-gray-400 block mb-0.5">Weight</span>
				<p class="font-bold text-gray-800">
					{shipment.dimensions.weight}
					<span class="text-[10px] font-medium text-gray-400 ml-0.5"
						>{shipment.dimensions.weightMetrics}</span
					>
				</p>
			</div>
		</div>

		<div class="flex items-start gap-3">
			<div class="text-lg opacity-40">💎</div>
			<div>
				<span class="text-gray-400 block mb-0.5">Fragility</span>
				<p class="font-bold text-gray-800">{fragilityMap[shipment.details.fragility]}</p>
			</div>
		</div>

		<div class="flex items-start gap-3">
			<div class="text-lg opacity-40">🚛</div>
			<div>
				<span class="text-gray-400 block mb-0.5">Access</span>
				<p class="font-bold text-gray-800">{accessMap[shipment.details.access]}</p>
			</div>
		</div>
	</div>
</div>
