<!-- OptionGroup.svelte -->
<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    export let options = [
        { title: 'Option 1', description: 'Description for option 1', state: 'unselected'},
        { title: 'Option 2', description: 'Description for option 2', state: 'unselected'},
        { title: 'Option 3', description: 'Description for option 3', state: 'unselected'}
    ];
    export let optionSelected = false
    export let selectedTitle = ''

    $: _optionSelected = optionSelected;

    function handleClick(option: { title: string, description: string, state: string }) {
        if (_optionSelected || option.state === 'selected' || option.state === 'disabled') {
            return;
        } else {
            optionSelected = true;
            selectedTitle = option.title;
            option.state = 'selected';
            dispatch('click', option);
        }
    }
</script>


<div class="option-group">
    {#each options as option}
        <div class="max-w-[max-content] flex items-center">
            <button class="{(_optionSelected && selectedTitle === option.title) || (option.state === 'selected')  ? 'pill-button selected' : (_optionSelected || option.state === 'disabled') ? 'pill-button disabled' : 'pill-button'} rounded-3xl px-5 py-0 flex items-center"
                on:click={() => handleClick(option)}
            >
                {option.title}
            </button>
            <span class="description">{option.description}</span>
        </div>
    {/each}
</div>

<style>
    .option-group {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
    }
    .description {
        margin-left: 0.5rem;
    }
    .pill-button {
        background-color: #EB8486;
        transition: background-color 0.2s ease;
        color: white;
        scale: 95%;
        cursor: pointer;
    }
    .pill-button:hover {
        background-color: #EB5352;
        scale: 105%;
    }
    .pill-button.selected {
        background-color: #EB5352;
        scale: 95%;
        font-weight: bold;
        cursor: default;
    }
    .pill-button.disabled {
        background-color: #cfcfcf;
        scale: 95%;
        cursor: default;
    }
</style>