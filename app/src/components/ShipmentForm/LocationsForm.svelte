<script lang="ts">
	import { sleep } from '$src/utils/utils';
	import { defaultLocation, pickedLocations } from '$stores/locationsPick';
	import { ValidationMessage, reporter } from '@felte/reporter-svelte';
	import { validator } from '@felte/validator-yup';
	import { createForm } from 'felte';
	import { LngLat } from 'maplibre-gl';
	import * as yup from 'yup';

	import type { LocationsFormInterface } from './interfaces';
	import { locationsFormSchema as schema } from './schemas';

	export let initialValues: LocationsFormInterface;

	export let onSubmit;
	export let onBack;
	export let showModal = true;

	let lastRequestTime = Date.now();
	let lastSrcLocation = defaultLocation;
	let lastDestLocation = defaultLocation;

	let srcLocationDisabled = true;
	let dstLocationDisabled = true;

	// maybe store there some default location or cache some.
	$: src = $pickedLocations.src;
	$: dst = $pickedLocations.dst;

	$: if (src.toString() != lastSrcLocation.toString()) {
		srcLocationDisabled = true;
		lastSrcLocation = src;

		setFields('sourceLocationLat', src.lat, true);
		setFields('sourceLocationLng', src.lng, true);

		fetchLocation(src)
			.then((name) => setFields('sourceName', name))
			.catch((_) => {
				srcLocationDisabled = false;
				$data.sourceName = 'default';
			});
	}

	$: if (dst.toString() != lastDestLocation.toString()) {
		dstLocationDisabled = true;
		lastDestLocation = dst;

		setFields('destinationLocationLat', dst.lat, true);
		setFields('destinationLocationLng', dst.lng, true);

		fetchLocation(dst)
			.then((name) => setFields('destinationName', name))
			.catch((_) => {
				dstLocationDisabled = false;
				$data.destinationName = 'default';
			});
	}

	const fetchLocation = async (lngLat: LngLat): Promise<string> => {
		await sleep(2 + Math.floor(Math.random() * 7));

		const currentTime = Date.now();
		const timeSinceLastRequest = currentTime - lastRequestTime;

		if (timeSinceLastRequest < 1000) {
			await sleep(1000 - timeSinceLastRequest);
		}

		lastRequestTime = Date.now();

		const response = await fetch(
			`https://nominatim.openstreetmap.org/reverse?lat=${lngLat.lat}&lon=${lngLat.lng}&format=geocodejson`
		);
		const parsed = await response.json();

		const geo = parsed.features[0].properties.geocoding;

		return geo.city + ', ' + geo.country;
	};

	const { form, data, setData, touched, setFields } = createForm<yup.InferType<typeof schema>>({
		extend: [reporter, validator({ schema })],
		onSubmit,
		initialValues
	});

	const sourceNameInitial = $data.sourceName;
	const destinationNameInitial = $data.destinationName;

	$: {
		if (sourceNameInitial !== $data.sourceName) {
			$touched.sourceName = true;
		}

		if (destinationNameInitial !== $data.destinationName) {
			$touched.destinationName = true;
		}
	}
</script>

<div>
	<h2
		class="font-bold text-xl bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent"
	>
		Logistics Route
	</h2>
	<p class="text-neutral-600 text-sm max-w-sm mt-2">Define pickup and dropoff points.</p>
	<!-- <div class="px-1">
		<h2
			class="font-black text-2xl bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent uppercase tracking-tight"
		>
			Logistics Route
		</h2>
		<p class="text-gray-400 text-[10px] font-black uppercase tracking-[0.2em] mt-1 opacity-70">
			Define pickup and dropoff points
		</p>
	</div> -->

	<form use:form class="mt-4">
		<div class="relative px-2">
			<div
				class="absolute left-[29px] top-8 bottom-8 w-0.5 border-l-2 border-dashed border-gray-100 z-0"
			></div>

			<div class="space-y-10 relative z-10">
				<div class="flex items-center gap-6 group">
					<div
						class="w-10 h-10 rounded-full border-2 border-primary bg-white shadow-sm flex items-center justify-center shrink-0 transition-transform group-hover:scale-110"
					>
						<div class="w-3 h-3 rounded-full bg-primary"></div>
					</div>
					<div
						class="flex-1 bg-gray-50/50 p-4 rounded-2xl border border-transparent group-hover:border-primary/10 group-hover:bg-white transition-all cursor-pointer"
						on:click={() => (showModal = false)}
					>
						<span class="text-[9px] font-black text-primary uppercase tracking-widest block mb-1"
							>From / Pickup</span
						>
						{#if $data.sourceName}
							<p class="text-sm font-bold text-gray-800 leading-tight truncate">
								{$data.sourceName}
							</p>
							<p class="text-[9px] text-gray-400 font-mono mt-1">
								{$data.sourceLocationLat.toFixed(4)}, {$data.sourceLocationLng.toFixed(4)}
							</p>
						{:else}
							<p class="text-sm font-medium text-gray-300 italic tracking-tight">
								Tap to set pickup location...
							</p>
						{/if}
					</div>
				</div>

				<div class="flex items-center gap-6 group">
					<div
						class="w-10 h-10 rounded-full bg-secondary shadow-lg flex items-center justify-center shrink-0 transition-transform group-hover:scale-110 ring-4 ring-secondary/5"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="w-5 h-5 text-white"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2.5"
								d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"
							/>
						</svg>
					</div>
					<div
						class="flex-1 bg-gray-50/50 p-4 rounded-2xl border border-transparent group-hover:border-secondary/10 group-hover:bg-white transition-all cursor-pointer"
						on:click={() => (showModal = false)}
					>
						<span class="text-[9px] font-black text-secondary uppercase tracking-widest block mb-1"
							>To / Destination</span
						>
						{#if $data.destinationName}
							<p class="text-sm font-bold text-gray-800 leading-tight truncate">
								{$data.destinationName}
							</p>
							<p class="text-[9px] text-gray-400 font-mono mt-1">
								{$data.destinationLocationLat.toFixed(4)}, {$data.destinationLocationLng.toFixed(4)}
							</p>
						{:else}
							<p class="text-sm font-medium text-gray-300 italic tracking-tight">
								Tap to set destination...
							</p>
						{/if}
					</div>
				</div>
			</div>
		</div>

		<div class="flex justify-center mt-8">
			<button
				type="button"
				on:click={() => (showModal = false)}
				class="flex items-center gap-3 px-6 py-2.5 bg-white border border-gray-100 rounded-full shadow-sm hover:shadow-md hover:border-accent/20 transition-all text-accent group"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="w-4 h-4 transition-transform group-hover:rotate-12"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2.5"
						d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"
					/>
				</svg>
				<span class="text-[11px] font-black uppercase tracking-widest">Select via Map</span>
			</button>
		</div>

		<div class="mt-6 space-y-2">
			{#each ['sourceName', 'destinationName'] as name}
				<ValidationMessage for={name} let:messages={message}>
					{#if message}
						<div
							class="mx-2 p-3 bg-red-50 rounded-2xl border border-red-100 flex items-center gap-3 animate-in fade-in slide-in-from-top-1"
						>
							<div
								class="w-1.5 h-1.5 rounded-full bg-red-500 shadow-[0_0_8px_rgba(239,68,68,0.5)]"
							></div>
							<p class="text-[10px] font-black text-red-600 uppercase tracking-tight leading-none">
								{message}
							</p>
						</div>
					{/if}
				</ValidationMessage>
			{/each}
		</div>

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
