<script>
    // import { createEventDispatcher } from 'svelte';

    // const dispatch = createEventDispatcher();

    export let bees = [];
    export let cursor = 0;

    $: grid = [
        [0, 1],
        [2, 3, 4],
        [5, 6],
    ];
    // $: dispatch('cursorchange', { cursor });
</script>

<div class="comb">
    {#each grid as row}
        <div class="comb__row">
            {#each row as cell}

            <div 
                class="comb__cell" 
                class:queen={cell === 3}
                class:focus={cursor === cell}
                data-idx={cell}
                on:click={()=>{cursor = cell}}
            >
                {bees[cell]}
            </div>
            {/each}
        </div>
    {/each}
</div>

<style scoped lang="scss">
    @use './variables';

    .comb {
        --cell-size: 5rem;
        --cell-gap: 0.5rem;

        display: flex;
        flex-direction: column;
        align-items: center;

        &__row {
            display: flex;
            gap: var(--cell-gap);
            margin-bottom: calc(var(--cell-gap) * -2);
        }

        &__cell {
            --fill: var(--surface-secondary);

            color: var(--text-primary);
            position: relative;
            width: var(--cell-size); 
            height: calc(var(--cell-size) / 3.46 * 2);
            background-color: var(--fill);
            margin: calc(var(--cell-size) / 3.46) 0;
            display: grid;
            place-items: center;
            font-size: 2em;
            font-weight: bold;
            cursor: pointer;
            touch-action: manipulation;
            text-transform: capitalize;

            &.focus {
                filter: brightness(.9);
            }

            &:before, &:after {
                content: "";
                position: absolute;
                width: 0;
                border-left: calc(var(--cell-size) / 2) solid transparent;
                border-right: calc(var(--cell-size) / 2) solid transparent;
            }

            &:before {
                bottom: 100%;
                border-bottom: calc(var(--cell-size) / 3.46) solid var(--fill);
            }

            &:after {
                top: 100%;
                left: 0;
                width: 0;
                border-top: calc(var(--cell-size) / 3.46) solid var(--fill);
            }

            &.queen {
                --fill: var(--yellow);
            }
        }
    }
</style>