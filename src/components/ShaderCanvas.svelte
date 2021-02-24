<!-- Basic Test Canvas  -->

<svelte:window bind:innerWidth={innerWidth} bind:innerHeight={innerHeight}/>

<script>

	import { onMount } from 'svelte';
	import { createProgramFromSources } from '../utils/ShaderUtils.svelte';

	export let scale = 1;
	export let xscrollPerc = 0;
	export let yscrollPerc = 0;

	let m = { x: 0, y: 0 };
	let time = 0;
	
	let canvas;
	let canvasWidth, canvasHeight;
	let clientWidth, clientHeight;
	let innerWidth, innerHeight;

	// Default fragment shader
	export let fragShader = `
		precision highp float;

		uniform vec2 u_resolution;
		uniform vec2 u_mouse;
		uniform float u_time;
		uniform float u_xscrollPerc;
		uniform float u_yscrollPerc;

		void main() {
			gl_FragColor = vec4(fract((gl_FragCoord.xy - u_mouse) / u_resolution), fract(u_time), 1);
		}
	`;

	function handleMousemove(event) {

		let s = (scale * 100);

		canvasWidth = s + "%";
		canvasHeight = s + "%";

		m.x = (event.clientX / clientWidth) * s;
		m.y = (event.clientY / clientHeight) * s;
	}

	onMount(() => {
		const gl = canvas.getContext('webgl');
		let frame;

		if (!gl) {
			return; // Fallback options for no webgl support
		}

		const vs = `
			attribute vec4 a_position;
			void main() { gl_Position = a_position; }
		`;

		const program = createProgramFromSources(gl, [vs, fragShader]);
		const positionAttributeLocation = gl.getAttribLocation(program, "a_position");

		const resolutionLocation = gl.getUniformLocation(program, "u_resolution");
		const mouseLocation = gl.getUniformLocation(program, "u_mouse");
		const timeLocation = gl.getUniformLocation(program, "u_time");
		const xscrollLocation = gl.getUniformLocation(program, "u_xscrollPerc");
		const yscrollLocation = gl.getUniformLocation(program, "u_yscrollPerc");

		const positionBuffer = gl.createBuffer();
		gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
		gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 1, -1, -1, 1, -1, 1, 1, -1, 1, 1]), gl.STATIC_DRAW);

		(function loop() {
			frame = requestAnimationFrame(loop);
			time += .01;

			gl.viewport(.95, 0, gl.canvas.width, gl.canvas.height);
			gl.useProgram(program);
			gl.enableVertexAttribArray(positionAttributeLocation);
			gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
			gl.vertexAttribPointer(positionAttributeLocation, 2, gl.FLOAT, false, 0, 0);

			gl.uniform2f(resolutionLocation, gl.canvas.width, gl.canvas.height);
			gl.uniform2f(mouseLocation, m.x, -m.y);
			gl.uniform1f(timeLocation, time);
			gl.uniform1f(yscrollLocation, yscrollPerc);
			gl.uniform1f(xscrollLocation, xscrollPerc);

			gl.drawArrays(gl.TRIANGLES, 0, 6);
		}());

		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>

<style>
	div {
		width: 100%;
		height: 100%;
		position: fixed;
		left: 0;
		top: 0;
	}
	canvas {
		width: 100%;
		height: 87%;
		background-color: #333;
		position: fixed;
		left: 0;
		top: 13%;
	}
</style>

<div on:mousemove={handleMousemove} bind:clientWidth={clientWidth} bind:clientHeight={clientHeight}>
	<canvas 
		bind:this={canvas}
		width={canvasWidth}
		height={canvasHeight}
	></canvas>
</div>