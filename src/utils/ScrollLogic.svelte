<!-- Uniforms settings controlled by the Scrolling of the page -->
<!-- Scroll Logic seperated, makes it easier to update later if needed -->

<script context="module">
	import { dba41 as data } from "../scatterform/ScatterformLitePresets.svelte";

	export function getCoords(mouse) {
		let coords = { x: 0, y: 0 };
		coords.x = mouse.x.map(0, 1, 1.8, 1.3);
		coords.y = mouse.y.map(0, 1, 0.78, 0.6);

		return coords;
    }

	export function getScrollY(yscroll) {
        let v = yscroll;
        if(yscroll > .94){
            v = yscroll.map(.94, 1, v, 1.2);
        }
        return (v * 4) + 2.3875292;
    }
    
    export const getColorMod3 = yscroll => .7;
    export const getColorMod4 = yscroll => .01147;
    
    export function getCamZoom(yscroll){
        return 23.4;
    }

    export function getPiMod(yscroll){
        return data.pimod;
    }
    export function getLayer2Amp(yscroll){
        return data.layer2Amp;
    }
    export function getMod1A(yscroll){
        return data.mod1A * ((1-yscroll));
    }
    export function getMod1B(x, yscroll){
        if(yscroll < .82) return data.mod1B;
        return (data.mod1B * (3-x)) - 200;
    }
    export function getMod1C(yscroll){
        return data.mod1C;
    }
    export function getMod1D(yscroll){
        return data.mod1D * ((2-yscroll));
    }

    export function getCamFov(yscroll){
        return data.cam_fov;
    }
    export function getLayer4Amp(yscroll){
        return data.layer4Amp;
    }
    export function getLightZ(yscroll){
        return data.light1Z;
    }
    export function getGlowStrength(yscroll){
        return data.glowStrength * ((1-yscroll));
    }

    export function getGlowColor(yscroll){
        return {r: 0.549, g: 0.21568, b: yscroll};
    }
    export function getColor2(yscroll){
        return {r: 0.8, g: 0, b: 0};
    }
    export function getColor3(yscroll){
        return {r: 0, g: 0.513, b: 0.8};
    }
    export function getColor4(yscroll){
        return {r: 0.196, g: yscroll, b: 0.67};
    }
</script>