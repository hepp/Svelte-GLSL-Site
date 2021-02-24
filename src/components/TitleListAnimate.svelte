<!-- Basic component to display several messages animated into a single line -->

<script>

    export let shown;
    export let list;

	const ranSymbols = ["%","@","#","/"];
	
	let count = -30;
	let level = 0;
	let tick = 0;
	let outtro = 0;

	// Header Title
	const newHeader = () => Math.floor(Math.random() * list.length);
	let chars = 0;
	let selected = newHeader();
	let text = "";
	let cursor = "";

	(function loop() {
        requestAnimationFrame(loop);

		// Quick and simple blinking cursor effect
		if(tick % 66 == 0 || text.length < 2){
			cursor = "  ";
		}else if(tick % 66 == 22){
			cursor = " :";
		}
		tick++;

		// Header title changes
		count++;
		if(count > 0){

			if(level == 0){

				// Animate in 
				if(shown && count % 2 == 1 && chars < list[selected].length){
					text += list[selected].charAt(chars);
					chars++;
				}
				// Hide 
				if(!shown || (count > 200 && list.length > 1)){
					level = 1;
					count = 0;
					chars = 0;
				}

			} else if(level == 1){

				// Outro
				if(text.length > 0){

					// Quick and simple text scrable effect

					if(outtro > 8){
						// Remove lines
						text = text.substring(0, text.length - 1);
					}

					if(tick % 2 == 1){
						// Add random chars effect
						let rSel = Math.floor(Math.random() * text.length);
						if(rSel > 2){
							let textS = text.substring(0, rSel-1);
							textS += ranSymbols[Math.floor(Math.random() * ranSymbols.length)];
							textS += text.substring(rSel, text.length);
							text = textS;
						}
					}

					if(text.length < 1 || outtro > 60) {
						// Exit outtro
						text = "";
					}

					outtro++;
					
				// Select new
				}else{

					// Reset state
					outtro = 0;
					tick = 0;
					level = 0;
					count = -Math.floor(Math.random() * 30);
					
					let hNew = 0;
					while(hNew == 0 || hNew == selected){
						hNew = newHeader();
					}
					selected = hNew;
				}
			}
        }
    })();
</script>

<style>
	h2 {
		position: relative;
		left: 20px;
        padding: 0 1.15em 0 1.15em;
		color:rgb(200, 195, 188);
		background-color: rgb(53, 104, 126);
		-webkit-transition: background-color 250ms linear;
		-ms-transition: background-color 250ms linear;
		transition: background-color 250ms linear;
        pointer-events: none;
	}
	.selected {
		background-color: #004daa;
		-webkit-transition: background-color 700ms linear;
		-ms-transition: background-color 700ms linear;
		transition: background-color 700ms linear;
	}
</style>

<div>
	<h2 class="{shown ? 'selected' : ''}">{"> " + text + cursor}</h2>
</div>