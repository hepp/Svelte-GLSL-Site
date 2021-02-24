<!-- WebGL Harness for 'Lite' version of 'Scatterform' pixelshader by Hepp Maccoy -->

<script>
	import { onMount } from "svelte";

	import {
		createProgramFromSources,
		bindQuadBuffer,
		vertexShader,
	} from "../utils/ShaderUtils.svelte";

	import { fragShader } from "../scatterform/ScatterformLiteFrag.svelte";

	export let scale = 1;
	export let camZoom = 30;
	export let camFov = -0.41;
	export let piMod = 3.14;
	export let colorMod3 = .5;
	export let colorMod4 = .5;
	export let layer2Amp = 1;
	export let layer4Amp = 0;
	export let mod1A = 0;
	export let mod1B = 0;
	export let mod1C = 0;
	export let mod1D = 0;
	export let time = 0;
	export let camRotate = { x: 0, y: 0 };
	export let color2 = {r: 1, g: 0, b: 0};
	export let color3 = {r: 0, g: 1, b: 0};
	export let color4 = {r: 1, g: 0, b: 1};
	export let colorGlow = {r: .5, g: .5, b: 0};
	export let glowStrength = 0;
	export let lightZ = 0;
	
	export let fallbackUrl = "https://get.webgl.org/"; // Default URL

	export let canvasWidth = 2600;  // initial dimensions before resized
	export let canvasHeight = 1500;

	let canvas;
	let gl;
	let program;
	let resolution_Loc;
	let time_Loc;
	let camRotateX_Loc, camRotateY_Loc;
	let camZoom_Loc, camFov_Loc;
	let piMod_Loc;
	let mod1A_Loc, mod1B_Loc, mod1C_Loc, mod1D_Loc;
	let layer2_Loc, layer4_Loc;
	let glowColor_Loc, glowStrength_Loc;
	let color2_Loc, color3_Loc, color4_Loc;
	let colorMod3_Loc, colorMod4_Loc;
	let light1Z_Loc;
	let frame;

	onMount(() => {
		gl = canvas.getContext("webgl") || canvas.getContext("experimental-webgl");
		if (!gl) {window.location.replace(fallbackUrl); return; }

		program = createProgramFromSources(gl, [vertexShader, fragShader]);
		gl.useProgram(program);
		bindQuadBuffer(gl, gl.getAttribLocation(program, "a_position"));

		setupUniforms();

		(function loop() {
			frame = requestAnimationFrame(loop);

			gl.uniform2f(resolution_Loc, gl.canvas.width, gl.canvas.height);
			updateUniforms();

			gl.drawArrays(gl.TRIANGLES, 0, 6);
		})();

		return () => cancelAnimationFrame(frame);
	});

	function setupUniforms() {
		resolution_Loc = gl.getUniformLocation(program, "resolution");
		time_Loc = gl.getUniformLocation(program, "time");
		camRotateX_Loc = gl.getUniformLocation(program, "u_cam_rotateX");
		camRotateY_Loc = gl.getUniformLocation(program, "u_cam_rotateY");
		camZoom_Loc = gl.getUniformLocation(program, "u_cam_zoom");
		camFov_Loc = gl.getUniformLocation(program, "u_cam_fov");
		piMod_Loc = gl.getUniformLocation(program, "u_pimod");
		mod1A_Loc = gl.getUniformLocation(program, "u_mod1A");
		mod1B_Loc = gl.getUniformLocation(program, "u_mod1B");
		mod1C_Loc = gl.getUniformLocation(program, "u_mod1C");
		mod1D_Loc = gl.getUniformLocation(program, "u_mod1D");
		layer2_Loc = gl.getUniformLocation(program, "u_layer2Amp");
		layer4_Loc = gl.getUniformLocation(program, "u_layer4Amp");
		glowColor_Loc = gl.getUniformLocation(program, "u_glowColor");
		glowStrength_Loc = gl.getUniformLocation(program, "u_glowStrength");
		color2_Loc = gl.getUniformLocation(program, "u_color2");
		color3_Loc = gl.getUniformLocation(program, "u_color3");
		color4_Loc = gl.getUniformLocation(program, "u_color4");
		colorMod3_Loc = gl.getUniformLocation(program, "u_colorMod3");
		colorMod4_Loc = gl.getUniformLocation(program, "u_colorMod4");
		light1Z_Loc = gl.getUniformLocation(program, "u_light1Z");
	}

	function updateUniforms() {
		gl.uniform1f(time_Loc, time);
		gl.uniform1f(camFov_Loc, camFov);
		gl.uniform1f(camZoom_Loc, camZoom);
		gl.uniform1f(camRotateX_Loc, camRotate.x);
		gl.uniform1f(camRotateY_Loc, camRotate.y);
		gl.uniform1f(piMod_Loc, piMod);
		gl.uniform1f(colorMod3_Loc, colorMod3);
		gl.uniform1f(colorMod4_Loc, colorMod4);
		gl.uniform1f(layer2_Loc, layer2Amp);
		gl.uniform1f(layer4_Loc, layer4Amp);
		gl.uniform1f(mod1A_Loc, mod1A);
		gl.uniform1f(mod1B_Loc, mod1B);
		gl.uniform1f(mod1C_Loc, mod1C);
		gl.uniform1f(mod1D_Loc, mod1D);
		gl.uniform1f(light1Z_Loc, lightZ);
		gl.uniform1f(glowStrength_Loc, glowStrength);
		gl.uniform3f(glowColor_Loc, colorGlow.r, colorGlow.g, colorGlow.b);
		gl.uniform3f(color2_Loc, color2.r, color2.g, color2.b);
		gl.uniform3f(color3_Loc, color3.r, color3.g, color3.b);
		gl.uniform3f(color4_Loc, color4.r, color4.g, color4.b);
	}
</script>

<style>
	div {
		width: 100%;
		height: 100%;
	}
	canvas {
		width: 100%;
		height: 100%;
		background-color: #333;
		position: fixed;
	}
</style>

<div
	bind:clientWidth={canvasWidth}
	bind:clientHeight={canvasHeight}>
	<canvas
		bind:this={canvas}
		width={canvasWidth * scale}
		height={canvasHeight * scale} />
</div>
