<!-- Basic Test Shader -->

<script>
    export let yscrollPerc = 0;
    export let xscrollPerc = 0;

	import ShaderCanvas from "./ShaderCanvas.svelte";

	let scale = 1;

	const fragShader = `
		precision highp float;

		uniform vec2 u_resolution;
		uniform vec2 u_mouse;
		uniform float u_time;
		uniform float u_xscrollPerc;
		uniform float u_yscrollPerc;

		void main() {

			float scale = ` + (scale * 100) + `.;

			float p1 = (u_yscrollPerc * ((scale * .01) * 400.)) - gl_FragCoord.y;
			if(p1 > -1.5 && p1 < scale){
				gl_FragColor = vec4(0, 1, 1, 1);
			}else if(p1 > scale && p1 < (scale*2.)){
				gl_FragColor = vec4(0, 0, 1, 1);
			}else if(p1 > (scale*2.) && p1 < (scale*3.)){
				gl_FragColor = vec4(1, 0, 1, 1);
			}
			else{
				gl_FragColor = vec4(fract((gl_FragCoord.xy - u_mouse) / u_resolution), fract(u_time), 1);

			}
		}
    `;
    
</script>

<ShaderCanvas {xscrollPerc} {yscrollPerc} {fragShader} {scale} />

