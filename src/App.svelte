<svelte:head>
	
</svelte:head>

<svelte:window bind:scrollY={scrollY} bind:innerHeight={innerHeight} bind:innerWidth={innerWidth}/>

<script>
	import { mobileAndTabletCheck } from "./utils/Utils.svelte"
	if (mobileAndTabletCheck()) window.location.replace(fallbackUrl);

	import { easeTo } from "./utils/Utils.svelte";
	import NumberMap from "./utils/NumberMap.svelte";

	import Content from "./components/Content.svelte";
	import Title from "./components/Title.svelte";
	import RotatingItemGrid from "./components/RotatingItemGrid.svelte";
	import TitleListAnimate from "./components/TitleListAnimate.svelte";
	import ImageGrid from "./components/ImageGrid.svelte";
	import ScatterformControl from "./components/ScatterformControl.svelte";
	import ButtonLink from "./components/ButtonLink.svelte";

	import { thingsList,
		brandsList,
		portfolioList,
		codeItems,
		wwwItems,
		wwwList,
		fallbackUrl,
		imgDataLogos,
		imgDataPortfolio } from "./utils/Data.svelte";
	import { getCoords, getScrollY } from "./utils/ScrollLogic.svelte";


	let scrollY;
	let innerHeight;
	let innerWidth;
	let yscrollPerc = 1000;

	const yTargets = [0, .185, .42, .94];
	let yShowing = [false, false, false, false];
	let yShownSec = [0, 0, 0, 0];
	let yLevels = [0, 0, 0, 0];

	let mouse = { x: 0, y: 0 };
	let m = { x: 0, y: 0 };

	let coords = { x: 0, y: 0 };
	let ease = { x: 0, y: 0 };

	let camRotate = { x: 0, y: 0 };
	
	(function loop() {
		requestAnimationFrame(loop);
		yscrollPerc = scrollY / (document.body.scrollHeight - innerHeight);

		// Mouse paralax
		m.x = (((1-camRotate.x)+1) * 20) + 10;
		m.y = ((1-mouse.y) * 60) - 30;
		
		// Scroll hide/shown logic for sections
		for (const i in yTargets) {
			let dist = Math.abs(yscrollPerc - yTargets[i]);
			if(yShowing[i]){
				if(dist > .1) yShowing[i] = false;
				yShownSec[i] = 0;
			}else{
				if(dist < .08){
					yShownSec[i]++;
					if(yShownSec[i] > 20) yShowing[i] = true;
				}
			}
			yLevels[i] = ((document.body.scrollHeight - document.body.clientHeight) * yTargets[i]) + (document.body.clientHeight * .5);
		}

		coords = getCoords(mouse, yscrollPerc);
		ease.x = easeTo(ease.x, coords.x, .97);
		ease.y = easeTo(ease.y, coords.y, .75);

		camRotate.x = ease.x;
		camRotate.y = ease.y - getScrollY(yscrollPerc);

	}());

	function handleMousemove(event) {
		mouse.x = Math.min( event.clientX / innerWidth, 1);
		mouse.y = event.clientY / innerHeight;
	}

</script>

<style>
	:global(body) {
		overflow-x: hidden;
		margin: 0;
		padding: 0;
	}
	.shown {
		position: absolute;
	}
</style>

<div on:mousemove={handleMousemove} >

	<ScatterformControl {yscrollPerc} scale={.5} {mouse} {camRotate} {fallbackUrl} />
	<Content /> 

	<div class="shown" style="transform: translate({m.x}px, {m.y + yLevels[0]}px)">
		<TitleListAnimate shown={yShowing[0]} list={thingsList}/>	
	</div>
	<div class="shown" style="transform: translate({m.x * 4}px, {m.y + yLevels[0] + 60}px)">
		<RotatingItemGrid shown={yShowing[0]} {innerWidth} list={codeItems}/>
	</div>
	<div class="shown" style="transform: translate({m.x}px, {m.y + yLevels[1] - 100}px)">
		<TitleListAnimate shown={yShowing[1]} list={brandsList}/>
	</div>
	<div class="shown" style="transform: translate({(m.x * 7) - 20}px, {m.y + yLevels[1]}px)">
		<ImageGrid shown={yShowing[1]} imgData={imgDataLogos}/>
	</div>
	<div class="shown" style="transform: translate({m.x}px, {m.y + yLevels[2] - 100}px)">
		<TitleListAnimate shown={yShowing[2]} list={portfolioList}/>
	</div>
	<div class="shown" style="transform: translate({(m.x * 7) - 20}px, {m.y + yLevels[2]}px)">
		<ImageGrid shown={yShowing[2]} imgData={imgDataPortfolio}/>
	</div>
	<div class="shown" style="transform: translate({(m.x * 9) - 80}px, {m.y + yLevels[2] + 300}px)">
		<ButtonLink text={"Visit Heppmaccoy.com Portfolio"} link={"https://www.heppmaccoy.com"} shown={yShowing[2]}/>
	</div>
	<div class="shown" style="transform: translate({m.x}px, {m.y + yLevels[3] - 30}px)">
		<TitleListAnimate shown={yShowing[3]} list={wwwList}/>
	</div>
	<div class="shown" style="transform: translate({m.x * 4}px, {m.y + yLevels[3] + 30}px)">
		<RotatingItemGrid shown={yShowing[3]} {innerWidth} list={wwwItems}/>
	</div>
	<div class="shown" style="transform: translate({(m.x * 5) - 50}px, {m.y + yLevels[3] + 300}px)">
		<ButtonLink text={"GitHub: Svelte+WebGL Example"} link={"https://github.com/hepp/Svelte-GLSL"} shown={yShowing[3]}/>
		<ButtonLink text={"GitHub: This page"} link={"https://github.com/hepp/Svelte-GLSL-Site"} shown={yShowing[3]}/>
	</div>

	<Title {camRotate} />
</div>

