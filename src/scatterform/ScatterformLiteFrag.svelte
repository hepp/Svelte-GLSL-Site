<script context="module">

	// Original Scatterform Shader by AudioPixel/Hepp Maccoy
	// Raymarching Math Functions by IQ/Iquilezles
	export const fragShader = 
		`#ifdef GL_ES
		precision mediump float;
		#endif

		uniform float time;
		uniform vec2 resolution;

		//---

		// Scatterform API (Lite version)

		uniform float u_cam_zoom;
		uniform float u_cam_fov;

		uniform float u_cam_rotateX;
		uniform float u_cam_rotateY;
		
		uniform float u_pimod;

		uniform float u_mod1A;
		uniform float u_mod1B;
		uniform float u_mod1C;
		uniform float u_mod1D;

		uniform float u_layer2Amp;
		uniform float u_layer4Amp;

		uniform vec3 u_glowColor;
		uniform float u_glowStrength;

		uniform vec3 u_color2;
		uniform vec3 u_color3;
		uniform vec3 u_color4;

		uniform float u_colorMod3;
		uniform float u_colorMod4;
		
		uniform float u_light1Z;

		//---

		vec3 glow;
		vec3 q;
		vec3 col1;
		vec3 col2;
		vec3 col3;
		vec3 col4;
		#define PI 3.14159265358979323846
		
		float sinc( float x, float k ) {
			float a = PI * (float(k)*x-1.0);
			return sin(a)/a;
		}
		float soc(vec3 p) {
			vec3 n = normalize(sign(p+1e6));
			return min(min(dot(p.xy, n.xy), dot(p.yz, n.yz)), dot(p.xz, n.xz));
		}
		mat2 r2d(float a) {
			float sa=sin(a);
			float ca=cos(a);
			return mat2(ca,sa,-sa,ca);
		}
		vec2 amod(vec2 p, float m) {
			float a=mod(atan(p.x,p.y), m)-m*.5;
			return vec2(cos(a), sin(a))*length(p);
		}
		vec2 mo(inout vec2 p, vec2 d) {
			vec2 q = p;
			q.x = abs(q.x) - d.x;
			q.y = abs(q.y) - d.y;
			if (q.y > q.x) q = q.yx;
			return q;
		}

		float map(vec3 p) {
			float d = 1.;
			float a = abs(p.y);

			p.yz *= r2d(sign(a) * u_cam_rotateY);
			p.xz *= r2d(sign(a) * u_cam_rotateX);

			// Mod1
			p.xz = mo(p.xz, vec2(u_mod1A, u_mod1B));
			p.zx = mo(p.xz, vec2(u_mod1C, u_mod1D));

			// Repeat using PI 
			p.xz = amod(p.xz, (PI * u_pimod) / 3.0);

			// Repeat 
			float spreadZ = 9.1829;
			p.z = mod(p.z, spreadZ)-(spreadZ *.5);

			float spreadX = 1.3426;
			p.x = mod(p.x, spreadX)-(spreadX *.5);
			p.y = mod(p.y + 3., 10.1057) - 5.;

			d = min(d, soc(max(abs(p) - 0.2283, 0.0028)));

			glow += u_glowColor * 0.0401 / (0.0067 + d*d); // color

			return length(p * -0.0211) * 1.0 - (d * -1.0);
		}
		
		vec3 calcNormal(in vec3 p) {
			vec2 e = vec2(0, -1.0) * 0.005;
			return normalize( e.xyy * map(p + e.xyy) + e.yyx * map(p + e.yyx) + e.yxy * map(p + e.yxy) + e.xxx * map(p + e.xxx) );
		}

		void main() {

			int section = 1;

			// Position
			vec2 st = (gl_FragCoord.xy / resolution.xy) * 2.01 - 1.;
			st.x *= resolution.x / resolution.y;
			
			// Ray origin
			vec3 ro = vec3(st, u_cam_zoom);

			// Ray distance
			vec3 rd = normalize(vec3(st + vec2(0.), u_cam_fov));

			// Map point
			vec3 mp;
			mp = ro;

			// Map distance
			float md;
			
			// Ray march
			for (int i=0; i<17; i++) {
				md = map(mp);

				if (md < 0.04535) break;
				mp += rd * md;
			}

			// Define our 2 deck levels (0-1) 
			float b = length(ro - mp);
			float deckA = 1.1755 - (b * 0.0379) * 0.9951;
			float deckB = 1.1755 - (b * 0.03) * 0.7696;
			vec3 l;

			// Use sin curve to modify base levels
			deckA = sinc(deckA, 1.0);
			deckB = sinc(deckB, 1.0);

			// Light1
			vec3 light = vec3(8., 0., u_light1Z);
			vec3 p = ro + rd * (mp);
			vec3 normal = calcNormal(p);

			if (md < 0.0972) {
				float dif = clamp(dot(normal, normalize(light - p)), 0., 1.);
				dif *= 5.0 / dot(light - p, light - p);
				l = vec3(pow(dif, 0.3398));
			} else {
				l = vec3(-0.2918);
			}

			// AudioPixel color blending
			vec3 c1 = vec3(deckA * -1.);
			vec3 c2 = vec3(deckB * u_layer2Amp);
			col1 = c1 + c2 - 2.0 * c1 * c2;
			col2 = mix(vec3(0.), u_color2, l.x);
			col3 = mix(vec3(0.), u_color3, (p.x * .06) * u_layer4Amp);
			vec3 c = (col1 + col2) - col3;

			c = c * (3. - 2. * c); // contrast
			c = mix(c, vec3(c.x + c.y + c.z) * .33, u_colorMod3 ); // desat
			c *= 1. + u_colorMod4 * vec3(u_color4); // tint 

			vec3 gt = c + (glow * u_glowStrength);
			c *= gt;

			gl_FragColor = vec4(c, 1.);
		}`;

</script>