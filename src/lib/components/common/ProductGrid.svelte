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
    export let showProductDetailsModal = false; // New state variable to manage modal
    export let selectedProductImages: string[] = [];
    export let generic_description = "This is placeholder text for the product description. It should be replaced with actual product information. Will span 2 lines";

    $: _productSelected = productSelected;

    function handleViewDetailsClick(product: { name: string, description: string, image: string, state: string }) {
      if (product.state === 'selected' || product.state === 'disabled') {
        return;
      } else {
        productSelected = true;
        selectedTitle = product.name;
        product.state = 'selected';
        showProductDetailsModal = true; // Show the modal
        selectedProductImages[0] = product.image;
        selectedProductImages[1] = product.image;
      }
    }

    function handleCloseClick() {
        showProductDetailsModal = false;
        const selectedProduct = products.find(p => p.state === 'selected');
        if (selectedProduct) {
            selectedProduct.state = 'unselected';
            productSelected = false;
            selectedTitle = '';
            selectedProductImages = [];
        }
    }
</script>

{#if showProductDetailsModal}
    <div class="modal">
        <div class="carousel-container">
<!--            <button class="close-button" on:click={() => (showProductDetailsModal = false)}>Close</button>-->
            <button class="close-button" on:click={() => handleCloseClick()} >Close</button>
            <!-- Carousel -->
            {#each selectedProductImages as image}
                <img src={image} alt="Product Image" class="carousel-image" />
            {/each}
            <button class="url-button" on:click={() => window.open(product.url, '_blank')}>Go to Product</button>
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
                class="absolute top-0 right-0 flex cursor-pointer px-1 py-1 rounded-xl hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                on:click={() => handleViewDetailsClick(product)}
                >
                    <div class=" m-auto self-center">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" viewBox="0 0 24 24" id="magnifying-glass">
                          <path fill="#333" d="M9.37395 6.4078C8.5036 6.44165 7.71765 6.75215 7.2972 7.1727C7.10195 7.368 6.78535 7.368 6.5901 7.17275C6.3948 6.97755 6.39475 6.66095 6.58995 6.46565C7.2387 5.81685 8.2944 5.44905 9.33505 5.40855C10.3825 5.3678 11.5261 5.6546 12.3269 6.4556C12.5221 6.65085 12.5221 6.96745 12.3268 7.1627C12.1316 7.35795 11.815 7.3579 11.6197 7.16265C11.0742 6.617 10.2375 6.3742 9.37395 6.4078Z"></path>
                          <path fill="#333" fill-rule="evenodd" d="M14.4678 13.692C15.4238 12.5604 16 11.0974 16 9.5C16 5.91015 13.0898 3 9.5 3C5.91015 3 3 5.91015 3 9.5C3 13.0898 5.91015 16 9.5 16C11.0974 16 12.5604 15.4238 13.692 14.4678L13.5 15.5721L17.2568 19.3289L19.3289 17.2568L15.5721 13.5L14.4678 13.692ZM9.5 14.5C12.2614 14.5 14.5 12.2614 14.5 9.5C14.5 6.73858 12.2614 4.5 9.5 4.5C6.73858 4.5 4.5 6.73858 4.5 9.5C4.5 12.2614 6.73858 14.5 9.5 14.5Z" clip-rule="evenodd"></path>
                          <path fill="#333" d="M17.9639 20.0361L20.0361 17.9639L20.7139 18.6418C21.0954 19.0233 21.0954 19.6418 20.7139 20.0233L20.0233 20.7139C19.6418 21.0954 19.0233 21.0954 18.6418 20.7139L17.9639 20.0361Z"></path>
                        </svg>
                    </div>
                </button>
<!--                <button-->
<!--                    class="{(product.state === 'selected') ? 'pill-button selected' : 'pill-button'}"-->
<!--                    on:click={() => handleViewDetailsClick(product)}-->
<!--                    >-->
<!--                    Details-->
<!--                </button>-->
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
        position: relative;
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
    background: white;
    padding: 1rem;
    border-radius: 8px;
    max-width: 90%;
    max-height: 90%;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.carousel-image {
    max-width: 100%;
    max-height: 80vh;
    margin-bottom: 1rem;
}

.close-button {
    position: absolute;
    top: 1rem;
    right: 1rem;
    background: red;
    color: white;
    border: none;
    border-radius: 50%;
    width: 2rem;
    height: 2rem;
    cursor: pointer;
}
</style>