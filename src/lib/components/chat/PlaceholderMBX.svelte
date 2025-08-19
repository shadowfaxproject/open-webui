<script lang="ts">
	import { toast } from 'svelte-sonner';
	import { marked } from 'marked';

	import { onMount, getContext, tick, createEventDispatcher } from 'svelte';
	import { blur, fade } from 'svelte/transition';

	const dispatch = createEventDispatcher();

	import { config, user, models as _models, temporaryChatEnabled } from '$lib/stores';
	import { sanitizeResponseContent, extractCurlyBraceWords } from '$lib/utils';
	import { WEBUI_BASE_URL } from '$lib/constants';

	import SuggestionsMB from './SuggestionsMB.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';
	import EyeSlash from '$lib/components/icons/EyeSlash.svelte';
	import MessageInput from './MessageInput.svelte';

	const i18n = getContext('i18n');

	export let transparentBackground = false;

	export let createMessagePair: Function;
	export let stopResponse: Function;

	export let autoScroll = false;

	export let atSelectedModel: Model | undefined;
	export let selectedModels: [''];

	export let history;

	export let prompt = '';
	export let files = [];

	export let selectedToolIds = [];
	export let selectedFilterIds = [];

	export let imageGenerationEnabled = false;
	export let codeInterpreterEnabled = false;
	export let webSearchEnabled = false;

	export let toolServers = [];

	let models = [];

	let selectedModelIdx = 0;

	let prompt_suggestions = [
		{
				"title": [
						"📊 Homework Station Setup",
						""
				],
				"content": "Suggest some gifts for my child's homework station setup.",
				"pin": false,
				"in_season": true,
				"chat_id": "b2220d33868e6ae86a9b695e9b82fed2"
		},
		{
				"title": [
						"🎒 College Dorm Room Decor",
						""
				],
				"content": "what can i get something for my sister's college dorm room?",
				"pin": true,
				"in_season": true,
				"chat_id": "f97824cf9fd3a9249e45b691d62989e3"
		},
		{
				"title": [
						"🧗 Gift for Outdoor enthusiast",
						""
				],
				"content": "What will be a good gift for someone who likes outdoor activities?",
				"pin": false,
				"in_season": true,
				"chat_id": "dd7675fcf0d87e58b70728decefa46d5"
		},
		{
				"title": [
						"🎮 Nephew who loves video games",
						""
				],
				"content": "My nephew is a big fan of video games. Can you suggest a gift for him?",
				"pin": false,
				"in_season": true,
				"chat_id": "b28d30decd6bc671b9e6deb9c5a258c7"
		},
		{
				"title": [
						"☕️ Gift for coffee lover",
						""
				],
				"content": "I need to get a gift for my friend who is a big coffee drinker.",
				"pin": false,
				"in_season": true,
				"chat_id": "b5bc2f07e858b96d7001cbe1c6f6a42b"
		},
		{
				"title": [
						"📖 Gift for avid reader",
						""
				],
				"content": "I need to get a gift for my friend who loves to read.",
				"pin": false,
				"in_season": true,
				"chat_id": "1118f30ee6c11d516e126c416fe8f2e7"
		},
	];


	onMount(() => {});
</script>

<!--<div class="h-full w-full flex flex-col justify-center items-center">-->

<div class="m-auto w-full max-w-6xl px-2 @2xl:px-20 min-h-screen text-center mt-8 flex-col">
	<div
		class="w-full text-3xl text-gray-800 dark:text-gray-100 text-center flex items-center gap-4 font-primary"
	>
		<div class="w-full flex flex-col justify-center items-center">
			<div class="flex flex-row justify-center gap-3 @sm:gap-3.5 w-fit px-5">
				<div class="flex shrink-0 justify-center">
					<div class="flex -space-x-4 mb-0.5" in:fade={{ duration: 100 }}>
						{#each models as model, modelIdx}
							<Tooltip
								content={(models[modelIdx]?.info?.meta?.tags ?? [])
									.map((tag) => tag.name.toUpperCase())
									.join(', ')}
								placement="top"
							>
								<button
									on:click={() => {
										selectedModelIdx = modelIdx;
									}}
								>
									<img
										crossorigin="anonymous"
										src={model?.info?.meta?.profile_image_url ??
											($i18n.language === 'dg-DG'
												? `/doge.png`
												: `${WEBUI_BASE_URL}/static/favicon.png`)}
										class=" size-9 @sm:size-10 rounded-full border-[1px] border-gray-100 dark:border-none"
										alt="logo"
										draggable="false"
									/>
								</button>
							</Tooltip>
						{/each}
					</div>
				</div>

				<div class=" text-3xl @sm:text-3xl line-clamp-1 text-gray-500" in:fade={{ duration: 100 }}>
					{$i18n.t('Great gift suggestions for all occasions!!!')}
					<br />
					<div class=" text-sm @sm:text-3xl line-clamp-1 text-gray-500" in:fade={{ duration: 100 }}>
						{$i18n.t('*Some product links might be amazon-affiliate links.')}
						<a href="/affiliate-disclosure.html" target="_blank"><span>{$i18n.t('(Learn More)')}</span></a>
					</div>
				</div>
			</div>

			<div class="flex mt-1 mb-2">
				<div in:fade={{ duration: 100, delay: 50 }}>
					{#if models[selectedModelIdx]?.info?.meta?.description ?? null}
						<Tooltip
							className=" w-fit"
							content={marked.parse(
								sanitizeResponseContent(models[selectedModelIdx]?.info?.meta?.description ?? '')
							)}
							placement="top"
						>
							<div
								class="mt-0.5 px-2 text-sm font-normal text-gray-500 dark:text-gray-400 line-clamp-2 max-w-xl markdown"
							>
								{@html marked.parse(
									sanitizeResponseContent(models[selectedModelIdx]?.info?.meta?.description)
								)}
							</div>
						</Tooltip>

						{#if models[selectedModelIdx]?.info?.meta?.user}
							<div class="mt-0.5 text-sm font-normal text-gray-400 dark:text-gray-500">
								By
								{#if models[selectedModelIdx]?.info?.meta?.user.community}
									<a
										href="https://openwebui.com/m/{models[selectedModelIdx]?.info?.meta?.user
											.username}"
										>{models[selectedModelIdx]?.info?.meta?.user.name
											? models[selectedModelIdx]?.info?.meta?.user.name
											: `@${models[selectedModelIdx]?.info?.meta?.user.username}`}</a
									>
								{:else}
									{models[selectedModelIdx]?.info?.meta?.user.name}
								{/if}
							</div>
						{/if}
					{/if}
				</div>
			</div>

			<div class="text-base font-normal @md:max-w-3xl w-full py-3 {atSelectedModel ? 'mt-2' : ''}">
			</div>
		</div>
	</div>
	<div class="mx-auto max-w-2xl font-primary mt-2 flex flex-col" in:fade={{ duration: 200, delay: 200 }}>
		<SuggestionsMB
			suggestionPrompts={atSelectedModel?.info?.meta?.suggestion_prompts ??
				models[selectedModelIdx]?.info?.meta?.suggestion_prompts ??
				$config?.default_prompt_suggestions ??
				prompt_suggestions ??
				[]}
			inputValue={prompt}
		/>
	</div>
</div>
