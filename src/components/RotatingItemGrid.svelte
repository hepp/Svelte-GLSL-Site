<!-- Rotating List of Text Items -->

<script>

	import { fade, fly } from 'svelte/transition';

	export let shown;
	export let list;
	export let innerWidth;

	const defaultCount = 18;
	const newSelected = () => Math.floor(Math.random() * defaultCount);
	const delayTime = 90;
	let newDelay = 200;
	let selected = newSelected();
	let stage = 0;
	let count = 0;
	let reveal = 0;
	let columns;
	let prevWidth;
	let tNew;
	let width;

	let items = [];
	let displayed = [];
	for (let i = 0; i < defaultCount; i++) {
		items.push({"name": list[i], "x": 0, "y": 0, "size": 23, "visible": false});
		displayed.push(true);
	}
	for (let i = defaultCount; i < list.length; i++) {
		displayed.push(false);
	}

	(function loop() {
		requestAnimationFrame(loop);

		// Simple grid layout 

		width = Math.min(innerWidth, 1200);
		
		let xStart = 100;
		if(width < 900){
			columns = 2;
			xStart = 0;
		}else{
			columns = 3;
		}
		if(prevWidth != width){
			let xStack = (width - xStart) / columns;
			let xS = xStart;
			let yStart = 0;
			let yS = yStart;
			let cS = 0;

			for (let i = 0; i < defaultCount; i++) {
				items[i].x = xS;
				items[i].y = yS;
				yS += 42;

				cS++;
				if(cS >= defaultCount / columns){
					xS += xStack;
					yS = yStart;
					cS = 0;
				}
			}
		}
		prevWidth = width;

		if(shown){
			count++;

			if(reveal < items.length){

				// Intro
				if(count % 5 == 1){
					items[Math.floor(reveal)].visible = true;
					reveal++;
				}
			}else{

				// Things rotate randomly
				if(stage == 0){

					if(count >= newDelay){
						stage = 1;
						count = 0;
						items[selected].visible = false;
						newDelay = Math.floor(Math.random() * delayTime) + 25;
					}

				}else if(stage == 1){

					if(count >= 26){
						stage = 2;
						count = 0;

						tNew = Math.floor(Math.random() * list.length);
						while(displayed[tNew]){
							tNew = Math.floor(Math.random() * list.length);
						}
						displayed[list.indexOf(items[selected].name)] = false;
						items[selected].visible = true;
						items[selected].name = list[tNew];
						displayed[tNew] = true;
					}

				}else if(stage == 2){

					if(count >= 22){
						stage = 0;
						count = 0;
						let newS = 0;

						while(newS == 0 || newS == selected){
							newS = newSelected();
						}
						selected = newS;
					}
				}
			}
		}else{
			stage = 0;
			count = 0;
			reveal = Math.max(reveal - .5, 0);
			items[Math.floor(reveal)].visible = false;
		}
	})();
</script>

<style>
	div {
		opacity: .8;
        pointer-events: none;
	}
	
	h2 {
		position: absolute;
		width: 230px;
		background-color: #131516;
		color: rgb(200, 195, 188);
        padding: 0 .85em 0 .5em;
		font-size: .15em;
	}
</style>

<div>
	{#each items as o, i}
		{#if o.visible}
			<h2 in:fly="{{ x: -24, y: -1, duration: 230 }}" out:fade="{{ duration: 350 }}"
			style="font-size: {o.size}px; transform: translate({o.x}px,{o.y}px);">{o.name}</h2>
		{/if}
	{/each}
</div>