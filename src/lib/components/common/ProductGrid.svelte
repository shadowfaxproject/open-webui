<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    import { mobile } from '$lib/stores';

    const dispatch = createEventDispatcher();



    export let products = [
        {
            name: 'Option 1',
            description: 'Description for product 1',
            image: '/path/to/image1.jpg',
            state: 'unselected'
        },
        {
            name: 'Option 2',
            description: 'Description for product 2',
            image: '/path/to/image2.jpg',
            state: 'unselected'
        },
        {
            name: 'Option 3',
            description: 'Description for product 3',
            image: '/path/to/image3.jpg',
            state: 'unselected'
        }
    ];

    export let product_context = { header_message: '', footer_message: '' };
    export let productSelected = false;
    export let selectedTitle = '';
    export let productUrl = '';
    export let showProductDetailsModal = false; // New state variable to manage modal
    export let selectedProductImages: string[] = [];
    export let generic_description = "This is placeholder text for the product description. It should be replaced with actual product information. Will span 2 lines";
    export let currentImageIndex = 0;
    export let touchStartX = 0;
    export let touchEndX = 0;


    $: _productSelected = productSelected;

    function handleClick(product: { name: string, description: string, image: string, state: string }) {
        console.log('##### handleClick', product);
        if (_productSelected || product.state === 'selected' || product.state === 'disabled') {
            return;
        } else {
            productSelected = true;
            selectedTitle = product.name;
            productUrl = product.url;
            product.state = 'selected';
            showProductDetailsModal = true; // Show the modal
            selectedProductImages[0] = "/image_cache/favicon.png";
            selectedProductImages[1] = "/image_cache/trial_box_icon.png";
            selectedProductImages[2] = "/image_cache/favicon.png";
            selectedProductImages[3] = "/image_cache/trial_box_icon.png";
            // JITU : Do we want to send this dispatch?
            // dispatch('click', product);
        }
    }

    function showNextImage() {
        console.log('##### showNextImage', currentImageIndex);
        currentImageIndex = (currentImageIndex + 1) % selectedProductImages.length;
        console.log('##### UPDATED showNextImage', currentImageIndex);
    }

    function showPreviousImage() {
        console.log('##### showPreviousImage', currentImageIndex);
        currentImageIndex = (currentImageIndex - 1 + selectedProductImages.length) % selectedProductImages.length;
        console.log('##### UPDATED showPreviousImage', currentImageIndex);
    }


    function handleTouchStart(event: TouchEvent) {
        touchStartX = event.touches[0].clientX;
    }

    function handleTouchMove(event: TouchEvent) {
        touchEndX = event.touches[0].clientX;
    }

    function handleTouchEnd() {
        if (touchStartX - touchEndX > 50) {
            showNextImage();
        } else if (touchEndX - touchStartX > 50) {
            showPreviousImage();
        }
    }
</script>

{#if showProductDetailsModal}
    <div class="modal">
        <div class="carousel-container">
            <button class="close-button" on:click={() => (showProductDetailsModal = false)}>Close</button>
            <!-- Carousel -->
            <div class="carousel" on:touchstart={handleTouchStart} on:touchmove={handleTouchMove} on:touchend={handleTouchEnd}>
                <button class="carousel-button prev" on:click={() => showPreviousImage()}>❮</button>
                <img src={selectedProductImages[currentImageIndex]} alt="Product Image" class="carousel-image" />
                <button class="carousel-button next" on:click={() => showNextImage()}>❯</button>
            </div>
            <div class="dots">
                {#each selectedProductImages as _, index}
                    <span
                        class="dot {index === currentImageIndex ? 'active' : ''}"
                        on:click={() => (currentImageIndex = index)}
                    ></span>
                {/each}
            </div>
            <button class="url-button" on:click={() => window.open(productUrl, '_blank')}>Buy</button>
        </div>
    </div>
{/if}


<div class="product-grid-container">
    <!-- Add text on top -->
    <!-- JITU - replace this text with actual header -->
    <p class="grid-header">Please select an product from the grid below:</p>

    {#if product_context.header_message}
        {product_context.header_message}
    {/if}

    <div class="product-grid">
        {#each products as product}
            <div class="grid-item">
                <!-- Display image -->
                <img src={product.thumbnail} alt={product.thumbnail} class="grid-item-image" />

                <!-- Display name -->
                <h3 class="grid-item-name">{product.name}</h3>

                <p class="grid-item-price">{product.price}</p>
                <button
                    class="{(_productSelected && selectedTitle === product.name) || (product.state === 'selected') ? 'pill-button selected' : (_productSelected || product.state === 'disabled') ? 'pill-button disabled' : 'pill-button'}"
                    on:click={() => handleClick(product)}
                    >
                    View Details
                </button>
            </div>
        {/each}
    </div>

    {#if product_context.footer_message}
        {product_context.footer_message}
    {/if}
</div>

<style>
    .product-grid-container {
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .grid-header {
        font-size: 1.2rem;
        font-weight: normal;
        margin-bottom: 1rem;
    }

    .product-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 1rem;
    }

    .grid-item {
        display: flex;
        flex-direction: column;
        text-align: left;
        border: 1px solid #ddd;
        border-radius: 8px;
        background-color: #f9f9f9;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Add shadow */
    }

    .grid-item-image {
        width: 100%;
        height: 200px;
        object-fit: cover;
        margin: 0rem 0 0.5rem 1rem;
        border-radius: 8px 8px 0 0; /* Optional: Rounded corners for the top of the image */
        margin: 0; /* Remove any margin */
    }

    .grid-item-name {
        font-size: 1rem;
        align-self: left;
        font-weight: bold;
        margin: 0.5rem 0 0.5rem 1rem;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;
        text-overflow: ellipsis;
        min-height: 2.4em; /* Ensures space for 2 lines of text */
        line-height: 1.2em; /* Adjust line height to match the text spacing */
    }

    .grid-item-description {
        font-size: 0.9rem;
        align-self: left;
        color: #555;
        margin: 0.5rem 0 0.5rem 1rem;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;
        text-overflow: ellipsis;
        min-height: 2.4em; /* Ensures space for 2 lines of text */
        line-height: 1.2em; /* Adjust line height to match the text spacing */
    }

    .grid-item-price {
        font-size: 1rem;
        align-self: left;
        font-weight: bold;
        margin: 0.5rem 0 0.5rem 1rem;
        margin: 1rem 0 1rem 1rem;
    }

    .pill-button {
        background-color: #eb5352;
        transition: background-color 0.2s ease;
        color: white;
        padding: 0.5rem 1rem;
        margin: 0rem auto 0.5rem;
        border: none;
        border-radius: 20px;
        cursor: pointer;
        padding: 0.3rem 0.8rem;
    }

    .pill-button:hover {
        background-color: #EB5352;
    }

    .pill-button.selected {
        background-color: #EB5352;
        font-weight: bold;
        cursor: default;
    }

    .pill-button.disabled {
        background-color: #aaaaaa;
        color: #7b7b7b;
        cursor: not-allowed;
    }

    .modal {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.8);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .carousel-container {
        position: relative;
        height: 320px;
        background: white;
        padding: 1rem;
        border-radius: 8px;
        max-width: 300px;
        max-height: 300px;
        overflow: hidden;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .close-button {
        position: absolute;
        top: 1rem;
        background: red;
        color: white;
        border: none;
        border-radius: 20px;
        width: 5rem;
        height: 2rem;
        cursor: pointer;
    }

    .carousel {
        position: relative;
        top: 2rem;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .carousel-image {
        max-width: 200px;
        max-height: 200px;
        margin-bottom: 1rem;
    }

    .url-button {
        position: absolute;
        bottom: 1rem;
        background: red;
        color: white;
        border: none;
        border-radius: 20px;
        width: 5rem;
        height: 2rem;
        cursor: pointer;
    }


    .carousel-button {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        background: rgba(0, 0, 0, 0.5);
        color: white;
        border: none;
        border-radius: 50%;
        width: 2rem;
        height: 2rem;
        cursor: pointer;
        z-index: 10;
    }

    .carousel-button.prev {
        left: 1rem;
    }

    .carousel-button.next {
        right: 1rem;
    }

    .dots {
        display: flex;
        justify-content: center;
        margin-top: 1rem;
    }

    .dot {
        width: 10px;
        height: 10px;
        margin: 0 5px;
        background-color: #ccc;
        border-radius: 50%;
        cursor: pointer;
    }

    .dot.active {
        background-color: #eb5352;
    }
</style>