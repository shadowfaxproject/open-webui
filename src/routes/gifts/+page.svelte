<script>
  import PlaceholderMBX from '$lib/components/chat/PlaceholderMBX.svelte';
</script>

<div
	class="h-screen max-h-[100dvh] transition-width duration-200 ease-in-out w-full max-w-full flex flex-col"
	id="chat-container"
>
	<div class=" pb-2.5 flex flex-col justify-between w-full flex-auto overflow-auto h-0 max-w-full z-10 scrollbar-hidden">
		<PlaceholderMBX />
	</div>
</div>
