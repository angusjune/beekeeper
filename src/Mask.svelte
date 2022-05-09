<script>
    import { fade } from 'svelte/transition';

    export let show = false;
</script>

{#if show}
<div class="mask" transition:fade={{duration: 150}} on:touchstart|stopPropagation={()=>{}} on:click={()=>show = false}></div>
{/if}

<style scoped lang="scss">
    @use './variables';
    .mask {
        @include variables.blurredSurface($color: opacify(variables.$background, 0.7));

        position: fixed;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
    }
</style>