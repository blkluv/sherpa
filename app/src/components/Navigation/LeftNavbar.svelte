<script lang="ts">
	import clsx from 'clsx';

	import { page } from '$app/stores';
	import { searchableShipments } from '$src/stores/searchableShipments';

	import { userStore } from '$src/stores/user';
	import { walletStore } from '$src/stores/wallet';
	import WalletMultiButton from '../Wallet/WalletMultiButton.svelte';
	import BoxIcon from './NavbarIcons/BoxIcon.svelte';
	import DashboardIcon from './NavbarIcons/DashboardIcon.svelte';
	import HomeIcon from './NavbarIcons/HomeIcon.svelte';
	import IssuedIcon from './NavbarIcons/IssuedIcon.svelte';
	import PlusIcon from './NavbarIcons/PlusIcon.svelte';
	import SendIcon from './NavbarIcons/SendIcon.svelte';
	import TrackIcon from './NavbarIcons/TrackIcon.svelte';

	let storeToSearchIn = searchableShipments;

	$: currentPage = $page.url.pathname;
	$: isNavbarOpen = false;

	$: carrierRoute =
		$userStore.carrier.registered && $walletStore.publicKey
			? `${$walletStore.publicKey.toString()}/incoming`
			: 'register';

	$: navigation = [
		{
			name: 'Home',
			link: '/',
			svg: HomeIcon
		},
		{
			name: 'Shippers',
			link: '/shipmentsMap',
			svg: BoxIcon,
			routes: [
				{
					name: 'Issued',
					link: '/shipmentsMap/issued',
					svg: IssuedIcon
				},
				{
					name: 'Create',
					link: '/shipmentsMap/create',
					svg: PlusIcon
				}
			]
		},
		{
			name: 'Forwarders',
			link: '/forwarder/bought',
			svg: SendIcon
		},

		{
			name: 'Carriers',
			link: '/carrier',
			svg: TrackIcon,
			routes: [
				{
					name: 'Dashboard',
					link: `/carrier/${carrierRoute}`,
					svg: DashboardIcon
				}
			]
		}
	];

	function handleSearchKeyUp(e: KeyboardEvent) {
		if ($storeToSearchIn.searchString && e.key == 'Enter') {
			storeToSearchIn.performSearch();
		} else if (!$storeToSearchIn.searchString) {
			storeToSearchIn.purgeFiltered();
		}
	}
</script>

<div
	class={clsx(
		'fixed z-10 top-5 transition-all duration-300',
		'left-5 right-20 md:left-1/2 md:right-auto md:w-1/3 xl:w-1/4 md:-translate-x-1/2',
		isNavbarOpen ? 'opacity-0 pointer-events-none' : 'opacity-100'
	)}
>
	<div class="relative group">
		<div
			class="absolute inset-0 bg-white shadow-xl border border-gray-100 rounded-xl transition-all group-focus-within:border-primary/40"
		></div>

		<div class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none z-10">
			<svg
				class="h-5 w-5 text-gray-400 group-focus-within:text-primary transition-colors"
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke="currentColor"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="2.5"
					d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
				/>
			</svg>
		</div>

		<input
			class="relative z-10 block w-full pl-11 pr-4 py-2.5 bg-transparent outline-none text-sm md:text-base font-medium text-gray-800 placeholder-gray-400"
			type="text"
			placeholder="Search shipments..."
			bind:value={$storeToSearchIn.searchString}
			on:keyup={handleSearchKeyUp}
		/>

		<div
			class="absolute bottom-0 left-4 right-4 h-0.5 bg-gradient-to-r from-primary to-secondary transform scale-x-0 group-focus-within:scale-x-100 transition-transform duration-300 rounded-full"
		></div>
	</div>
</div>

<nav class="hidden md:block">
	<div
		class="fixed left-5 w-14 z-10 bg-white rounded-full top-1/2 -translate-y-1/2 py-6 shadow-2xl border border-gray-50"
	>
		<div class="flex flex-col items-center space-y-4">
			{#each navigation as { name, link, svg, routes }}
				{@const isMainActive = currentPage.includes(link)}
				<div
					class={clsx(
						'flex flex-col items-center space-y-4 rounded-full p-2 transition-colors',
						isMainActive && routes ? 'bg-primary/5' : ''
					)}
				>
					<div class="group relative">
						<a href={link}>
							<svelte:component
								this={svg}
								className={clsx(
									'w-6 h-6 transition-all duration-200 hover:scale-120',
									currentPage === link ? 'fill-primary' : 'fill-gray-400 hover:fill-gray-600'
								)}
							/>
						</a>
						<span
							class="pointer-events-none absolute left-14 top-0 opacity-0 group-hover:opacity-100 transition-opacity bg-gray-900 text-white text-[10px] px-2 py-1 rounded whitespace-nowrap z-50"
						>
							{name}
						</span>
					</div>

					{#if routes && isMainActive}
						<div class="flex flex-col space-y-3 pt-2 border-t border-gray-100">
							{#each routes as route}
								<div class="group relative">
									<a href={route.link}>
										<svelte:component
											this={route.svg}
											className={clsx(
												'w-5 h-5 transition-all hover:scale-120',
												currentPage === route.link
													? 'fill-primary'
													: 'fill-gray-400 hover:fill-gray-500'
											)}
										/>
									</a>
									<span
										class="pointer-events-none absolute left-12 top-0 opacity-0 group-hover:opacity-100 transition-opacity bg-gray-800 text-white text-[10px] px-2 py-1 rounded whitespace-nowrap z-50"
									>
										{route.name}
									</span>
								</div>
							{/each}
						</div>
					{/if}
				</div>
			{/each}
		</div>
	</div>

	<div class="fixed top-5 right-7 z-40">
		<div class="origin-right">
			<WalletMultiButton onClose={() => {}} />
		</div>
	</div>
</nav>

<nav class="md:hidden">
	<button
		class="fixed z-50 right-4 top-4 p-3 rounded-xl bg-white shadow-xl border border-gray-100 active:scale-95 transition-all"
		on:click={() => (isNavbarOpen = !isNavbarOpen)}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			class="w-6 h-6 text-gray-800"
			fill="none"
			viewBox="0 0 24 24"
			stroke="currentColor"
		>
			{#if !isNavbarOpen}
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="2.5"
					d="M4 6h16M4 12h16M4 18h7"
				/>
			{:else}
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					stroke-width="2.5"
					d="M6 18L18 6M6 6l12 12"
				/>
			{/if}
		</svg>
	</button>

	{#if isNavbarOpen}
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<div
			class="fixed inset-0 z-30 bg-black/40 backdrop-blur-sm transition-opacity"
			on:click={() => (isNavbarOpen = false)}
		></div>
	{/if}

	<div
		class={clsx(
			'fixed right-0 top-0 z-40 h-screen w-[280px] bg-white shadow-2xl transition-transform duration-300 ease-out flex flex-col',
			isNavbarOpen ? 'translate-x-0' : 'translate-x-full'
		)}
	>
		<div class="flex-1 overflow-y-auto py-4 mt-16">
			<ul class="space-y-1 px-3">
				{#each navigation as { name, link, routes }}
					{@const isActive = currentPage === link || (routes && currentPage.includes(link))}
					<li>
						<a
							href={link}
							on:click={() => (isNavbarOpen = false)}
							class={clsx(
								'flex items-center justify-between px-4 py-3 rounded-lg font-bold transition-all',
								isActive ? 'bg-primary/10 text-primary' : 'text-gray-600 hover:bg-gray-50'
							)}
						>
							<span class="text-lg">{name}</span>
							{#if isActive}<div class="w-1.5 h-1.5 rounded-full bg-primary"></div>{/if}
						</a>

						{#if routes && currentPage.includes(link)}
							<div class="ml-4 mt-1 border-l-2 border-primary/20 flex flex-col space-y-1">
								{#each routes as route}
									<a
										on:click={() => (isNavbarOpen = false)}
										href={route.link}
										class={clsx(
											'pl-6 py-2 text-sm font-medium transition-colors',
											currentPage === route.link
												? 'text-primary font-bold'
												: 'text-gray-400 hover:text-gray-600'
										)}
									>
										{route.name}
									</a>
								{/each}
							</div>
						{/if}
					</li>
				{/each}
			</ul>
		</div>

		<div class="p-6 bg-white border-t border-gray-100 mt-auto items-center flex justify-center">
			<div class="relative w-full flex justify-center">
				<WalletMultiButton onClose={() => (isNavbarOpen = false)} />
			</div>
		</div>
	</div>
</nav>
