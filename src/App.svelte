<script>
    import { tick } from 'svelte';
    import { fade, slide, scale } from 'svelte/transition';
	import { quintOut } from 'svelte/easing';

    import Comb from './Comb.svelte';
    import Keyboard from './Keyboard.svelte';

    let bees = ['a','b','i','r','d','l','z']; // bees[3] is the queen bee
    $: queen = bees[3] || undefined;

    let cursor = 0;

    let loading = false;
    let showNoSearchResult = false;
    let showKeyboard = true;

    let candidates = [];

    async function fetchData() {
        const ENDPOINT = 'https://api.datamuse.com/words';
        const query = '?sp=' + encodeURIComponent(`????*,*${queen}*+${bees.join('')}'`);

        loading = true;

		const response = await fetch(ENDPOINT + query);
		const data = await response.json();
        console.log(data);
		candidates = data;

		await tick();
		loading = false;
		showNoSearchResult = true;
    }

    function onKeyboardInput(e) {
        const {key} = e.detail;
        const keyVal = key.toLowerCase();

        if (keyVal === 'backspace') {
            bees[cursor] = '';
        } else if (keyVal === 'enter') {
            if (bees.indexOf('') < 0) {
                // all cells are filled
                showKeybaord = false;
                fetchData();
            } else {
                // toastOn();
            }
        } else if (keyVal.match(/^[a-z]$/i)) {
            bees[cursor] = keyVal;
            cursor = cursor < 6 ? cursor + 1 : 0;
        }
    }
</script>

<svelte:window on:touchstart={()=>{}} />

<div class="container">
    <header class="header">
        <h1 class="header__title">Beekeeper</h1>
    </header>

    <main class="main">
        <Comb bind:cursor {bees} />
    </main>
    

    {#if showKeyboard}
    <div class="keyboard-wrap" transition:slide>
        <Keyboard {queen} {bees} on:input={onKeyboardInput} />
    </div>
    {/if}

</div>



<style lang="scss">
    @use './variables';

    :global(body) {
        font-family: -apple-system, 'Open Sans', 'Helvetica Neue', sans-serif;
        color: var(--text-primary);
        background-color: var(--background);
        -webkit-user-select: none;
        overflow: hidden;
    }

    .container {
        height: 100%;
        display: flex;
        flex-direction: column;
        overflow-x: hidden;
    }

    .main {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;		
		width: var(--width);
		margin: 0 auto;
	}
</style>