<script>
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let bees = [];
    export let queenIndex = 0;
    export let cursor = 0;

    $: grid = [
        [0, 1],
        [2, 3, 4],
        [5, 6],
    ];

    function clickCell(cell) {
        cursor = cell;
        dispatch('click', {cursor});
    }
</script>

<div class="comb">
    {#each grid as row}
        <div class="comb__row">
            {#each row as cell}
            <div
                class="comb__cell" 
                class:queen={cell === queenIndex}
                class:focus={cursor === cell}
                tabindex="0"
                on:click={()=>clickCell(cell)}
                on:focus={()=>cursor = cell}
            >
                <span class="comb__cell-inner">{bees[cell]}</span>
            </div>
            {/each}
        </div>
    {/each}
</div>

<style scoped lang="scss">
    .comb {
        --cell-size: 4rem;
        --cell-gap: 0.4rem;

        display: flex;
        flex-direction: column;
        align-items: center;
        transform: rotate(30deg);

        &__row {
            display: flex;
            gap: var(--cell-gap);
            margin-bottom: calc(var(--cell-gap) * -2);

            &:last-of-type {
                margin-bottom: 0;
            }
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
            transition: transform 0.15s ease-out;
            will-change: transform;

            &:active {
                transform: scale(.9);
            }

            &.focus, &:focus {
                outline: 0;
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

        &__cell-inner {
            transform: rotate(-30deg);
        }
    }
</style>