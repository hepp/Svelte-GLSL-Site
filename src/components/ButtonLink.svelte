<script>
	import { fade, fly } from 'svelte/transition';
    export let shown, text, link;

</script>

<style>
    a {
		position: relative;
		left: 50px;
		top: 50px;
        font-size: large;
        padding: 1.4em;
        color: rgb(84, 178, 255);
        background-color: rgb(141, 0, 71);
    }
</style>

{#if shown}
<a href={link} in:fly="{{ duration: 300, delay: 1700 }}" out:fade="{{ duration: 300 }}">
    {text}
</a>
{/if}