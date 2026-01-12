<script lang="ts">
	import { getBuyShipmentTx } from '$lib/forwarder';
	import { anchorStore } from '$src/stores/anchor';
	import { userStore } from '$src/stores/user';
	import { walletStore } from '$src/stores/wallet';
	import { web3Store } from '$src/stores/web3';
	import type { ApiShipmentAccount } from '$src/utils/account/shipment';
	import { getShipmentSummary } from '$src/utils/utils';
	import { useSignAndSendTransaction } from '$src/utils/wallet/singAndSendTx';
	import { PublicKey } from '@solana/web3.js';
	import { get } from 'svelte/store';
	import { createNotification, updateNotification } from '../Notification/notificationsStore';
	import SummaryWrapper from '../SummaryWrapper.svelte';
	import Modal from './Modal.svelte';

	export let showModal: boolean;
	export let shipmentAccount: ApiShipmentAccount;

	$: shipmentData = shipmentAccount.account;

	function isAccountNameValid(name: string | null): boolean {
		if (!name || name.length == 0 || name.length > 64) {
			return false;
		}
		return true;
	}

	async function handleBuyClick() {
		const { program } = get(anchorStore);
		const wallet = get(walletStore);
		const { connection } = get(web3Store);
		const {
			forwarder: { name }
		} = get(userStore);

		if (!$walletStore.publicKey) {
			showModal = false;
			walletStore.openModal();
			return;
		}

		if (!isAccountNameValid(name)) {
			createNotification({ text: 'Invalid name', type: 'failed', removeAfter: 5000 });
			return;
		}

		const tx = await getBuyShipmentTx(
			program,
			$walletStore.publicKey,
			new PublicKey(shipmentAccount.publicKey),
			new PublicKey(shipmentAccount.account.shipper),
			name!
		);

		const id = createNotification({ text: 'Buy', type: 'loading', removeAfter: undefined });

		try {
			const signature = await useSignAndSendTransaction(connection, wallet, tx);

			updateNotification(id, { text: 'Buy', type: 'success', removeAfter: 5000, signature });

			showModal = false;
		} catch (err) {
			updateNotification(id, { text: 'Buy', type: 'failed', removeAfter: 5000 });
		}
	}

	$: summaryData = getShipmentSummary(shipmentData);
</script>

<Modal bind:showModal on:backdropClick={() => (showModal = false)}>
	<div class="p-2">
		<h2
			class="text-xl font-black bg-gradient-to-r from-primary to-secondary bg-clip-text text-transparent text-center mb-8"
		>
			Buy Shipment
		</h2>

		<SummaryWrapper shipment={summaryData} />

		<!-- <hr class="my-7" /> -->

		{#if !$userStore.forwarder.registered}
			<div class="mt-10 space-y-3">
				<div class="flex items-center justify-between px-1">
					<h3 class="text-[11px] font-black text-gray-400 uppercase tracking-wider">
						New Forwarder Name
					</h3>
					<span
						class="text-[10px] text-secondary font-bold bg-secondary-50 px-2 py-0.5 rounded-full"
					>
						Required
					</span>
				</div>

				<div class="relative">
					<input
						class="w-full bg-gray-100/70 px-5 py-3.5 text-sm rounded-2xl border-2 border-transparent transition-all placeholder:text-gray-400 font-medium"
						type="text"
						bind:value={$userStore.forwarder.name}
						placeholder="Business or Courier name"
					/>
				</div>

				<p class="px-1 text-[10px] text-gray-400 leading-relaxed italic">
					You'll be registered automatically upon purchase.
				</p>
			</div>
		{/if}

		<div class="mt-10 space-y-4">
			<button
				class="w-full py-3 rounded-full bg-gradient-to-r from-primary to-secondary text-white text-sm font-black uppercase tracking-[0.2em] shadow-xl shadow-primary/20 hover:shadow-primary/40 hover:scale-[1.02] active:scale-95 transition-all duration-200"
				on:click={handleBuyClick}
			>
				Confirm Purchase
			</button>
		</div>
	</div>
</Modal>
