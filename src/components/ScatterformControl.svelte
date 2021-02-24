<!-- Harness for 'ScatterFormLite' -->

<script>
    import { spring } from 'svelte/motion';

    import { getColorMod3, getColorMod4,
		getCamZoom, getPiMod, getLayer2Amp, getLayer4Amp,
		getMod1A, getMod1B, getMod1C, getMod1D,
		getGlowStrength, getLightZ,
		getGlowColor, getColor2, getColor3, getColor4
	} from "../utils/ScrollLogic.svelte";

    import ScatterformLite from "./ScatterformLite.svelte";

    export let yscrollPerc = 0;
    export let scale = 1;
    export let fallbackUrl;
    export let camRotate = { x: 0, y: 0 };
    export let mouse = { x: 0, y: 0 };

    const camFovDefault = -.41;
    let camFov;
    let count = 0;

	let xmod = spring(.5, {
		stiffness: 0.01,
		damping: 0.08
    });

    let mod1D = 0;
    
    (function loop() {
        requestAnimationFrame(loop);
        count++;

        // Uniform control (in addition to ScrollLogic methods used below)


        // Shader Mod1D 
        const limitMod1 = .15;
        if(yscrollPerc < limitMod1 + .02){
            xmod.set( mouse.x.map(0, 1, 3, 5) * yscrollPerc.map(0, limitMod1, 3, 0) );
        }else{
            if(yscrollPerc > .6){
                xmod.set( mouse.x.map(0, 1, 5, 12) * yscrollPerc.map(.5, .8, 3, 0) );
            }else{
                xmod.set(0);
            }
        }
        mod1D = ($xmod + (-16));
        
        // Easter Egg at end twists FOV
        if(yscrollPerc > .9){
            camFov = yscrollPerc.map(.9, 1, camFovDefault, .45);
        }else{
            // Default
            camFov = camFovDefault;
        }
    })();

</script>

<style>
	.container {
		width: 100%;
		height: 100%;
		position: fixed;
		left: 0;
		top: 0;
	}
</style>

<div class="container" >
    <ScatterformLite
        {scale}
        {mod1D}
        {camRotate}
        {camFov}
        {fallbackUrl}
        
        piMod = {getPiMod(yscrollPerc)}
        camZoom = {getCamZoom(yscrollPerc)}
        color2 = {getColor2(yscrollPerc)}
        color3 = {getColor3(yscrollPerc)}
        color4 = {getColor4(yscrollPerc)}
        lightZ = {getLightZ(yscrollPerc)}
        colorGlow = {getGlowColor(yscrollPerc)}
        glowStrength = {getGlowStrength(yscrollPerc)}
        layer2Amp = {getLayer2Amp(yscrollPerc)}
        layer4Amp = {getLayer4Amp(yscrollPerc)}
        mod1A = {getMod1A(yscrollPerc)}
        mod1B = {getMod1B(mouse.x, yscrollPerc)}
        mod1C = {getMod1C(yscrollPerc)}
        colorMod3 = {getColorMod3(yscrollPerc)}
        colorMod4 = {getColorMod4(yscrollPerc)}
    />
</div>