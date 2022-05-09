<script>
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let queen = '';
    export let bees = [];

    const keys = [
        ["q", "w", "e", "r", "t", "y", "u", "i", "o", "p"],
        ["a", "s", "d", "f", "g", "h", "j", "k", "l"],
        ["enter", "z", "x", "c", "v", "b", "n", "m", "backspace"],
    ];
</script>

<div class="keyboard">
    {#each keys as row}
        <div class="row">
            {#each row as key}
                <button 
                    class="key"
                    class:action={!key.match(/^[a-z]$/i)} 
                    class:drone={bees.indexOf(key) > -1}
                    class:queen={queen === key}
                    on:click={()=>dispatch('input', { key })}
                >
                    {#if key == 'backspace'}
                    <span class="material-symbols-outlined">backspace</span>
                    {:else}
                    {key}
                    {/if}
                </button>
            {/each}
        </div>
    {/each}
</div>

<style scoped lang="scss">
    .keyboard {
        width: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 8px;
    }

    .row {
        width: 100%;
        display: flex;
        gap: 6px;
        justify-content: center;
    }

    .key {
        --key-line-height: 4;
        --background: var(--surface-secondary);
        --color: #3a3a3c;

        -webkit-appearance: none;
        text-transform: uppercase;
        color: var(--color);
        font-size: 0.75em;
        font-weight: bold;
        flex: 1;
        max-width: calc((100% - 54px) / 10);
        line-height: var(--key-line-height);
        overflow: hidden;
        border-radius: 4px;
        background: var(--background);
        border: 0;
        margin: 0;
        padding: 0;
        display: grid;
        place-items: center;
        touch-action: manipulation;

        &:active, &:focus {
            filter: brightness(.95);
            outline: 0;
        }

        &.action {
            max-width: none;
        }

        &.drone {
            filter: brightness(.8);
        }

        &.queen {
            filter: none;
            background: var(--yellow);
        }
    }

    @media (min-width: 640px) {
		.key {
            --key-line-height: 2.5;
        }
	}
</style>


