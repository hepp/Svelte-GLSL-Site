<!-- Simple Image Grid animates in cascade style -->

<script>
	import { fade, fly } from 'svelte/transition';

	export let shown;
	export let imgData;

	export let imgDuration = 200;
	export let imgDelay = 105;
	export let imgOffset = -16;
</script>

<style>
	div {
		position: relative;
		opacity: .8;
		max-width: 1100px;
	}
	img {
		width: 150px;
		height: 116px;
		margin: 8px;
		background-color: #ffffff;
	}
</style>

{#if shown}
	<div in:fly="{{ duration: 200 }}">
		{#each imgData as o, i}
			<img
			in:fly="{{ y: imgOffset, duration: imgDuration, delay: imgDelay * (i+2) }}"
			out:fade="{{ duration: 200, delay: (imgDelay * .5) * i }}"
			src={o.src} alt={o.alt} title={o.alt}>
		{/each}
	</div>
{/if}