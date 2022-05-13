<script>
    import { tick } from 'svelte';
    import { slide } from 'svelte/transition';
    import Comb from './Comb.svelte';
    import Keyboard from './Keyboard.svelte';
    import WordList from './WordList.svelte';
    import Mask from './Mask.svelte';
    import Modal from './Modal.svelte';

    // let bees = ['a','b','i','r','d','l','z']; // for testing
    let bees = ['','','','','','','']; 
    let queenIndex = 6;
    $: queen = bees[queenIndex] || '';

    let cursor = 0;

    let loading = false;
    let showNoSearchResult = false;
    let showKeyboard = true;
    let showError = false;

    let showModal = false;

    let candidates = [];

    async function fetchData() {
        const ENDPOINT = 'https://api.datamuse.com/words';
        const query = '?sp=' + encodeURIComponent(`????*,*${queen}*+${bees.join('')}`) + '&md=dr&ipa=1';

        loading = true;

		const response = await fetch(ENDPOINT + query);
		const data = await response.json();
        console.log(data);
        // sort by length of the word
		candidates = data.sort((a, b) => -(a.word?.length - b.word?.length));

		await tick();
		loading = false;
		showNoSearchResult = true;
    }

    function onKeyboardInput(e) {
        const {key} = e.detail;
        const keyVal = key.toLowerCase();

        showNoSearchResult = false;
        showError = false;

        if (keyVal === 'backspace') {
            const nextCursor = cursor > 0 ? cursor - 1 : 6;
            if (bees[cursor]) {
                bees[cursor] = '';
            } else {
                bees[nextCursor] = '';
                cursor = nextCursor;
            }
        } else if (keyVal === 'enter') {
            if (bees.indexOf('') < 0) {
                // all cells are filled
                candidates = [];
                showKeyboard = false;
                fetchData();
            } else {
                showError = true;
            }
        } else if (keyVal.match(/^[a-z]$/i)) {
            // haven't entered yet
            if (bees.indexOf(keyVal) < 0) {
                bees[cursor] = keyVal;
                cursor = cursor < 6 ? cursor + 1 : 0;
            }
        }
    }

    function onWindowKeyPress(e) {
        onKeyboardInput({detail: { key: e.key }});
    }

    let dict = {
        word: '',
        pron: '',
        defs: [],
    };

    async function onClickWord(e) {
        const { index } = e.detail;

        const {word, tags, defs} = candidates[index];

        await tick();
        dict.word = word;
        dict.pron = tags[tags.findIndex(value => value.indexOf('ipa_pron') >= 0)].replace('ipa_pron:', '');
        dict.defs = defs;

        showModal = true;
    }

    let scrollY = 0;

    function onScroll(e) {
        scrollY = e.target.scrollTop < 0 ? 0 : e.target.scrollTop;
    }

    let combTransform = '';

    $: {combTransform = `scale(max(${1 - scrollY / 200},0.7)) translateY(min(${scrollY * 1.2}px,50px))`;}
</script>



<svelte:window on:touchstart={()=>{}} on:keydown={onWindowKeyPress} />

<div class="container" on:scroll={onScroll} >
    <header class="header">
        <h1 class="header__title">Beekeeper</h1>
    </header>

    <main class="main">
        <div class="main__tip">
            Fill the letters into the hive
        </div>
        <div class="main__comb" class:error={showError} style:transform={combTransform}>
            <Comb bind:cursor {bees} {queenIndex} on:click={()=> showKeyboard = true} />
        </div>

        <WordList words={candidates} {loading} {showNoSearchResult} on:click={onClickWord} />
    </main>
    
    <div class="actions">
        <div class="actions__buttons">
            <button class="button button--icon" on:click={()=> showKeyboard = !showKeyboard}>
				{#if showKeyboard}
				<span class="material-symbols-outlined">keyboard_hide</span>
				{:else}
				<span class="material-symbols-outlined">keyboard</span>
				{/if}
			</button>
        </div>
        {#if showKeyboard}
        <div class="actions__keyboard" transition:slide>
            <div class="actions__keyboard-inner">
                <Keyboard {queen} {bees} on:input={onKeyboardInput} />
            </div>
        </div>
        {/if}
    </div>
    
</div>

<Mask bind:show={showModal} />
<Modal bind:show={showModal} {...dict} />



<style lang="scss">
    @use './variables' as *;
    @use './button';

    :global(html) {
        height: 100%;
    }

    :global(body) {
        -webkit-tap-highlight-color: transparent;
        font-family: -apple-system, 'Open Sans', 'Helvetica Neue', sans-serif;
        color: var(--text-primary);
        background-color: var(--background);
        height: 100%;
        margin: 0;
        padding: 0;
        -webkit-user-select: none;
        overflow: hidden;
    }

    .container {
        height: 100%;
        display: flex;
        flex-direction: column;
        overflow-x: hidden;
    }

    .header {
        @include blurredSurface();
        box-sizing: border-box;
        position: sticky;
        top: 0;
        width: 100%;
        padding: 0.5em;
        z-index: 1;

        &__title {
            font-size: 1.5em;
            margin: 0;
            padding: 0;
            text-align: center;
        }
    }

    .main {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;		
		width: var(--width);
		margin: 0 auto;
        width: 90%;
        max-width: 640px;
        margin: auto;
        padding-top: 2em;
        gap: 2em;

        &__tip {
            text-align: center;
            opacity: 0.5;
        }

        &__comb {
            &.error {
                animation: shake 400ms cubic-bezier(.68, -.6, .32, 1.6) 1;
                @keyframes shake {
                    0% { transform: translateX(10px); }
                    20% { transform: translateX(-10px); }
                    40% { transform: translateX(10px); }
                    60% { transform: translateX(-10px); }
                    100% { transform: translateX(0px); }
                }
            }
        }
	}
    
    .actions {
        position: sticky;
        bottom: 0;
        width: 100%;

        &__keyboard {
            @include blurredSurface();
            padding-top: 1em;
            padding-bottom: env(safe-area-offset-bottom, 1em);
            width: 100%;
        }

        &__keyboard-inner, &__buttons {
            width: 95%;
            max-width: 640px;
            margin: auto;
        }

        &__buttons {
            display: flex;
            flex-direction: row-reverse;
            padding: 1em 0;
        }
    }
</style>