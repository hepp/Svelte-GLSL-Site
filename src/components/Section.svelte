<!-- Reusable logic for a section within the viewable scroll area -->

<script>
	export let yscrollPerc;
	export let yperc;

	let showing = false;
	let shownSec = 0;
	let targetY;

	(function loop() {
		requestAnimationFrame(loop);

		let dist = Math.abs(yscrollPerc - yperc);

		if(showing){
			if(dist > .05) hideSection();
			shownSec = 0;
		}else{
			if(dist < .04){
				shownSec++;
				if(shownSec > 20) showSection();
			}
		}

		targetY = ((document.body.scrollHeight - document.body.clientHeight) * yperc) + (document.body.clientHeight * .5);

	}());

	function showSection(){
		showing = true;
	}

	function hideSection(){
		showing = false;
	}
</script>

<style>
	div {
		position: absolute;
		pointer-events: none;
	}
	.showing {
		background-color: burlywood;
	}
</style>

<div class:showing style="transform: translateY({targetY}px)">
	<slot />
</div>