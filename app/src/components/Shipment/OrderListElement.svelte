<script lang="ts">
	import { DF_MODULUS, decodeDecrypted } from '$src/sdk/sdk';
	import { walletStore } from '$src/stores/wallet';
	import type { ApiShipmentAccount } from '$src/utils/account/shipment';
	import { getLocalStorage } from '$src/utils/wallet/localStorage';
	import clsx from 'clsx';
	import { AES } from 'crypto-ts';
	import { createDiffieHellman } from 'diffie-hellman';
	import { createEventDispatcher } from 'svelte';

	export let shipmentAccount: ApiShipmentAccount;
	export let selectedAccount: string | undefined = undefined;
	export let showStatus = false;
	export let acceptCallback: () => void = () => {};

	let viewMessage = false;
	const dispatch = createEventDispatcher();
	const handleShowClick = (e: MouseEvent) => {
		dispatch('buttonClicked');
	};

	$: shipmentData = shipmentAccount.account;
	$: locations = shipmentData.shipment.geography;
	$: priority = getPriority(shipmentData.shipment.details.priority);
	$: statusNumber = shipmentData.status;
	$: status = getStatusString(statusNumber);
	$: isViewerShipper =
		$walletStore.publicKey && $walletStore.publicKey.toString() === shipmentData.shipper;

	$: messageNeeded = isViewerShipper && shipmentData.status >= 4;

	function getDecryptionKey(localStorageKey: string) {
		try {
			const privateKey = getLocalStorage<string>(localStorageKey);

			if (privateKey) {
				return Buffer.from(privateKey, 'hex');
			} else {
				return null;
			}
		} catch (err) {
			return null;
		}
	}

	let message = 'decrypting...';

	$: if (messageNeeded && viewMessage) {
		const privateKey = getDecryptionKey(`shipper${shipmentAccount.publicKey}`);

		if (privateKey) {
			const dh = createDiffieHellman(DF_MODULUS);
			dh.setPrivateKey(privateKey);
			dh.generateKeys();

			const secret = dh.computeSecret(
				Buffer.from(Uint8Array.from(shipmentAccount.account.channel.carrier))
			);

			message = decodeDecrypted(
				AES.decrypt(shipmentAccount.account.channel.data, secret.toString('hex')).words
			);
		} else {
			message = 'private key not found';
		}
	}

	function getStatusString(status: number) {
		switch (status) {
			case 5:
				return 'Delivered';
			case 4:
				return 'Accepted by carrier';
			case 3:
				return 'Offered to carrier';
			case 2:
				return 'Bought by forwarder';
			case 1:
				return 'Not yet bought';
			default:
				return 'Unknown';
		}
	}

	const getPriority = (priority: number) => {
		switch (priority) {
			case 5:
				return {
					name: 'Very high',
					color: 'text-red-800',
					bg: 'bg-red-100'
				};
			case 4:
				return {
					name: 'High',
					color: 'text-red-600',
					bg: 'bg-red-50'
				};
			case 3:
				return {
					name: 'Medium',
					color: 'text-orange-700',
					bg: 'bg-orange-100'
				};
			case 2:
				return {
					name: 'Low',
					color: 'text-green-700',
					bg: 'bg-green-50'
				};
			case 1:
				return {
					name: 'Very low',
					color: 'text-green-800',
					bg: 'bg-green-100'
				};
			default:
				return {
					name: 'Unknown',
					color: 'text-gray-600',
					bg: 'bg-gray-100'
				};
		}
	};

	const getStatusColor = (s: number) => {
		if (s >= 5) return 'bg-green-100 text-green-700';
		if (s >= 3) return 'bg-blue-100 text-blue-700';
		return 'bg-gray-100 text-gray-700';
	};
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<li
	on:click
	class={clsx(
		'group rounded-xl border transition-all duration-200 cursor-pointer w-full overflow-hidden mb-3 mt-1',
		selectedAccount === shipmentAccount.publicKey
			? 'ring-1 ring-secondary-50/40 border-transparent shadow-md'
			: 'bg-white border-gray-200 hover:border-primary/50 hover:shadow-sm'
	)}
>
	<div class="p-5">
		<div class="flex items-start justify-between">
			<div class="flex-1">
				<h3 class="text-lg font-bold text-gray-900 group-hover:text-primary transition-colors">
					{shipmentData.name}
				</h3>
			</div>
			<div class="text-right">
				<p
					class="text-lg bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent font-black tracking-tight"
				>
					{(shipmentData.price / 10 ** 9).toFixed(3)} SOL
				</p>
				<p class="text-[10px] text-gray-400 font-medium">SHIPMENT VALUE</p>
			</div>
		</div>

		<div
			class="mt-4 grid grid-cols-1 sm:grid-cols-2 gap-3 p-2 bg-gray-50 rounded-lg border border-gray-100"
		>
			<div class="flex flex-col">
				<span class="text-[10px] text-gray-400 uppercase font-bold tracking-widest">From</span>
				<span class="text-sm font-semibold text-gray-700 truncate">{locations.fromName}</span>
			</div>
			<div
				class="flex flex-col border-t sm:border-t-0 sm:border-l border-gray-200 pt-2 sm:pt-0 sm:pl-3"
			>
				<span class="text-[10px] text-gray-400 uppercase font-bold tracking-widest">To</span>
				<span class="text-sm font-semibold text-gray-700 truncate">{locations.toName}</span>
			</div>
		</div>

		<div
			class="mt-4 flex flex-wrap items-center justify-between gap-y-2 text-xs border-t border-gray-50 pt-3 px-1"
		>
			<span
				class={clsx(
					'px-2 py-0.5 rounded-full text-[10px] font-bold uppercase tracking-wider border border-current/10',
					priority.bg,
					priority.color
				)}
			>
				{priority.name}
			</span>

			<div class="flex items-center text-gray-500">
				<span class="mr-1.5 opacity-60">⚖️</span>
				<span class="font-medium">Penalty:</span>
				<span class="ml-1 text-gray-900 font-semibold">
					{shipmentAccount.account.shipment.penalty / 10 ** 9}
				</span>
			</div>

			<div class="flex items-center text-gray-500">
				<span class="mr-1.5 opacity-60">🛡️</span>
				<span class="font-medium">Insurance:</span>
				<span class="ml-1 text-gray-900 font-semibold">
					{shipmentAccount.account.shipment.collateral / 10 ** 9}
				</span>
			</div>

			{#if showStatus}
				<span
					class={clsx(
						'ml-auto px-2 py-0.5 rounded-full text-[10px] font-bold uppercase tracking-wider',
						getStatusColor(statusNumber)
					)}
				>
					{status}
				</span>
			{/if}
		</div>

		{#if messageNeeded}
			<div class="mt-4 pt-3 border-t border-dashed border-gray-200">
				{#if !viewMessage}
					<button
						class="text-xs font-bold text-primary hover:text-primary-dark uppercase tracking-tight flex items-center"
						on:click|once|stopPropagation={() => (viewMessage = true)}
					>
						<span class="mr-1">👁️</span> Click to decrypt message
					</button>
				{:else}
					<div class="bg-blue-50 p-2 rounded text-xs text-blue-800 font-mono italic">
						" {message} "
					</div>
				{/if}
			</div>
		{/if}

		<div class="mt-4 flex items-center justify-end gap-2 border-t border-gray-50 pt-3">
			<button
				class="px-3 py-1.5 text-[11px] font-bold text-gray-400 hover:text-gray-700 hover:bg-gray-100 rounded-lg transition-colors uppercase tracking-wider"
				on:click|stopPropagation={handleShowClick}
			>
				Details
			</button>

			{#if shipmentAccount.account.status == 4}
				<button
					class="px-4 py-1.5 text-[11px] font-bold bg-accent text-white rounded-lg shadow-sm hover:bg-accent/90 transition-all active:scale-95 uppercase tracking-wider"
					on:click|stopPropagation={acceptCallback}
				>
					Confirm
				</button>
			{/if}
		</div>
	</div>
</li>
