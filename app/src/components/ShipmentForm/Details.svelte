<script lang="ts">
	import NumberInput from '../Inputs/NumberInput.svelte';

	// Styl dla wspólnych klas selectów
	const selectClass =
		'w-full bg-gray-50/50 border border-gray-100 text-gray-800 text-sm rounded-xl block p-3 outline-none focus:border-primary/30 focus:bg-white transition-all font-medium appearance-none cursor-pointer';
</script>

<div class="space-y-6 px-1">
	<div class="grid grid-cols-1 md:grid-cols-2 gap-5 bg-white">
		<div class="flex flex-col space-y-2">
			<label for="count" class="text-[11px] font-bold text-gray-500 ml-1 uppercase tracking-tight">
				Item Count
			</label>
			<div class="relative">
				<NumberInput
					name="count"
					class="w-full h-11 bg-gray-50/50 border border-gray-100 rounded-xl px-4 font-bold focus:bg-white focus:border-primary/30 outline-none transition-all"
				/>
				<span
					class="absolute right-4 top-1/2 -translate-y-1/2 text-xs text-gray-400 font-bold uppercase tracking-tighter pointer-events-none"
				>
					Units
				</span>
			</div>
		</div>

		<div class="flex flex-col space-y-2 text-right md:text-left">
			<label
				for="priority"
				class="text-[11px] font-bold text-gray-500 mr-1 md:ml-1 uppercase tracking-tight"
			>
				Priority Level
			</label>
			<div class="relative">
				<select name="priority" required class={selectClass}>
					<option value="1">1 - Very low</option>
					<option value="2">2 - Low</option>
					<option value="3" selected>3 - Medium</option>
					<option value="4">4 - High</option>
					<option value="5">5 - Very high</option>
				</select>
				<div class="absolute right-4 top-1/2 -translate-y-1/2 pointer-events-none opacity-40">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="w-4 h-4"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="3"
							d="M19 9l-7 7-7-7"
						/>
					</svg>
				</div>
			</div>
		</div>

		<div class="flex flex-col space-y-2">
			<label
				for="fragility"
				class="text-[11px] font-bold text-gray-500 ml-1 uppercase tracking-tight"
			>
				Fragility
			</label>
			<div class="relative text-left">
				<select name="fragility" required class={selectClass}>
					<option value="1">Very fragile</option>
					<option value="2">Fragile</option>
					<option value="3" selected>Normal</option>
					<option value="4">Robust</option>
					<option value="5">Very robust</option>
				</select>
				<div
					class="absolute right-4 top-1/2 -translate-y-1/2 pointer-events-none opacity-40 text-left"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="w-4 h-4"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="3"
							d="M19 9l-7 7-7-7"
						/>
					</svg>
				</div>
			</div>
		</div>

		<div class="flex flex-col space-y-2">
			<label for="access" class="text-[11px] font-bold text-gray-500 ml-1 uppercase tracking-tight">
				Delivery Access
			</label>
			<div class="relative">
				<select name="access" required class={selectClass}>
					<option selected disabled value="0">Select access type</option>
					<option value="1">Only car</option>
					<option value="2">Truck up to 10t</option>
					<option value="3">Truck over 10t</option>
					<option value="4">Something else</option>
					<option value="5">Can't tell</option>
				</select>
				<div class="absolute right-4 top-1/2 -translate-y-1/2 pointer-events-none opacity-40">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="w-4 h-4"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="3"
							d="M19 9l-7 7-7-7"
						/>
					</svg>
				</div>
			</div>
		</div>
	</div>
</div>
