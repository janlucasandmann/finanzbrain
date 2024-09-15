<script lang="ts">
	import { onMount, getContext } from 'svelte';
	import { Confetti } from 'svelte-confetti';

	import { WEBUI_NAME, config } from '$lib/stores';

	import { WEBUI_VERSION } from '$lib/constants';
	import { getChangelog } from '$lib/apis';

	import Modal from './common/Modal.svelte';

	import { addNewMemory, updateMemoryById } from '$lib/apis/memories';


	const i18n = getContext('i18n');

	export let show = false;

	let changelog = null;

	onMount(async () => {
		const res = await getChangelog();
		changelog = res;
	});
</script>

<Modal bind:show>
	<div class="px-5 pt-4 dark:text-gray-300 text-gray-700">
		<div class="flex justify-between items-start">
			<div class="text-xl font-semibold">
				
			</div>
			<button
				class="self-center"
				on:click={() => {
					localStorage.version = $config.version;
					show = false;
				}}
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 20 20"
					fill="currentColor"
					class="w-5 h-5"
				>
					<path
						d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z"
					/>
				</svg>
			</button>
		</div>
		
	</div>

	<div class=" w-full p-4 px-5 text-gray-700 dark:text-gray-100">
		<div class=" overflow-y-scroll max-h-80 scrollbar-hidden" style="margin-top:-40px; padding-top:20px;">
			<div class="mb-3">
				<div class=" mb-3 pr-2">
							<div class="font-semibold text-xl mb-1 dark:text-white">
								Willkommen bei Finanzbrain!
							</div>
							<p style="margin-bottom:40px">Bevor wir starten, möchten wir dir ein paar Fragen stellen, um dir besser zu helfen.</p>




							<input id="firstvisitname" style="margin-bottom:10px; color:white; border:none; border-bottom:1px solid rgba(255,255,255,0.1); padding:0px; width:100%; background:transparent; padding-bottom:10px;" type="text" placeholder="Dein Name" />
							<input id="firstvisitage" style="margin-bottom:10px; color:white; border:none; border-bottom:1px solid rgba(255,255,255,0.1); padding:0px; width:100%; background:transparent; padding-bottom:10px;" type="text" placeholder="Dein Alter" />
							<input id="firstvisitgender" style="margin-bottom:10px; color:white; border:none; border-bottom:1px solid rgba(255,255,255,0.1); padding:0px; width:100%; background:transparent; padding-bottom:10px;" type="text" placeholder="Dein Geschlecht" />


				</div>
			</div>
		</div>
		<div class="flex justify-end pt-3 text-sm font-medium">
			<button
				on:click={() => {

					let name = document.getElementById("firstvisitname").value;
					let age = document.getElementById("firstvisitage").value;
					let gender = document.getElementById("firstvisitgender").value;

					let content = "Name: " + name + ", Alter: " + age + ", Geschlecht: " + gender;

					addNewMemory(localStorage.token, content).catch((error) => {
						toast.error(error);
						return null;
					});

					localStorage.version = $config.version;
					show = false;
				}}
				class=" px-4 py-2 bg-emerald-700 hover:bg-emerald-800 text-gray-100 transition rounded-lg"
			>
				<span class="relative">Lass uns starten</span>
			</button>
		</div>
	</div>
</Modal>
