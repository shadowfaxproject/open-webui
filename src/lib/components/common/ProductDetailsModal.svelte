<script lang="ts">
    import Carousel from '$lib/components/common/Carousel.svelte';
    import Modal from '$lib/components/common/Modal.svelte';
    import { createEventDispatcher } from 'svelte';

    export let product: any;

    const dispatch = createEventDispatcher();

    let show = true;

    $: if (!show) dispatch('close');
</script>

<Modal bind:show size="sm" containerClassName="p-0" className="bg-white dark:bg-gray-900 rounded-lg overflow-hidden">
    <div class="relative h-[400px] w-full">
        <button
            class="absolute top-2 left-2 z-10 px-1 py-1 rounded-sm bg-gray-50 border border-gray-200 text-gray-700 shadow-md"
            on:click={() => { show = false; }}
        >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
                stroke-width="2.5"
                class="size-3.5"
            >
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 6l12 12M6 18L18 6" />
            </svg>
        </button>
        <Carousel imageUrls={product?.thumbnails} showArrows={true} duration={3000} merchantName={product?.merchant_name}/>
    </div>
</Modal>
