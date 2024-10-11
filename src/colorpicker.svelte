<script>
	// export let name;
	// Declare variables for hue, saturation, and value
	let hue = 0;
  	let saturation = 100;
 	let value = 100;



	let hex = "#ff0000";
	let hex1;
	let rgbR = 255, rgbG = 0, rgbB = 0;
	let cmykC = 0, cmykM = 100, cmykY = 100, cmykK = 0;
	let hslH = 0, hslS = 100, hslL = 50;

	// Variables for picker position
	let pickerX = 50; // X position (Saturation)
    let pickerY = 50;   // Y position (Value)
	// Reactive declaration for background color based on hue
	$: backgroundColor = `hsl(${hue}, 100%, 50%)`;
	$: showcolor = `rgb(${rgbR},${rgbG},${rgbB})`;

	import convert from 'color-convert';
    import { rgb } from 'color-convert/conversions';


	
	function startDragging(event) {
		// Listen for mouse movements
    	document.addEventListener('mousemove', onMouseMove);
    	document.addEventListener('mouseup', stopDragging);
    	updatePickerPosition(event);
		onSatValChange();
	}
	
	function stopDragging() {
    	// Remove the mouse listeners
    	document.removeEventListener('mousemove', onMouseMove);
    	document.removeEventListener('mouseup', stopDragging);
 	}

  	function onMouseMove(event) {
    	updatePickerPosition(event);
  	}

  	function updatePickerPosition(event) {
    	const { left, top, width, height } = event.target.getBoundingClientRect();

    	// Calculate saturation and value based on mouse position
    	pickerX = Math.min(Math.max(0, event.clientX - left), width) / width * 100;
    	pickerY = Math.min(Math.max(0, event.clientY - top), height) / height * 100;

    	// Update saturation (horizontal) and value (vertical)
    	saturation = pickerX;
    	value = 100 - pickerY; // Invert Y axis since top is 100% and bottom is 0%
  	}

	function onHexInput(event) {
		const hexValue = event.target.value;
		if (isValidHex(hexValue)) {
			// hex = hexValue;
			// hex1 = "#" + hex;
			const rgb = convert.hex.rgb(hexValue);
			[rgbR, rgbG, rgbB] = rgb;
			const hsv = convert.rgb.hsv(rgbR, rgbG, rgbB);
			[hue, saturation, value] = hsv;
			const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);
			[hslH,hslS,hslL]= hsl;
			const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);
			[cmykC,cmykM,cmykY,cmykK]= cmyk;
		}
	}

	function onRgbInput() {
		[rgbR, rgbG, rgbB] = [clamp(rgbR, 0, 255), clamp(rgbG, 0, 255), clamp(rgbB, 0, 255)];
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);
		const hsv = convert.rgb.hsv(rgbR, rgbG, rgbB);
		[hue, saturation, value] = hsv;
		const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);
		[hslH,hslS,hslL]= hsl;
		const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);
		[cmykC,cmykM,cmykY,cmykK]= cmyk;
	}

	function onCmykInput() {
		[cmykC, cmykM, cmykY, cmykK] = [clamp(cmykC, 0, 100),clamp(cmykM, 0, 100),clamp(cmykY, 0, 100),clamp(cmykK, 0, 100)];
		const rgb = convert.cmyk.rgb(cmykC, cmykM, cmykY, cmykK);
		[rgbR, rgbG, rgbB] = rgb;
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);
		const hsv = convert.rgb.hsv(rgbR, rgbG, rgbB);
		[hue, saturation, value] = hsv;
		const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);
		[hslH,hslS,hslL]= hsl;
	}

	function onHslInput() {
		[hslH, hslS, hslL] = [clamp(hslH, 0, 360), clamp(hslS, 0, 100), clamp(hslL, 0, 100)];
		const rgb = convert.hsl.rgb(hslH, hslS, hslL);
		[rgbR, rgbG, rgbB] = rgb;
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);
		const hsv = convert.rgb.hsv(rgbR, rgbG, rgbB);
		[hue, saturation, value] = hsv;
		const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);
		[cmykC,cmykM,cmykY,cmykK]= cmyk;
    }

	function onHsvInput() {
		[hue, saturation, value] = [clamp(hue, 0, 360), clamp(saturation, 0, 100), clamp(value, 0, 100)];
		const rgb = convert.hsv.rgb(hue, saturation, value);
		[rgbR, rgbG, rgbB] = rgb;
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);
		const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);
		[hslH,hslS,hslL]= hsl;
		const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);
		[cmykC,cmykM,cmykY,cmykK]= cmyk;
	}

	function isValidHex(hex) {
		return /^#[0-9A-Fa-f]{6}$/.test(hex);
		}

		function clamp(value, min, max) {
		return Math.min(Math.max(value, min), max);
	}

	function onHueChange() {
		const rgb = convert.hsv.rgb(hue, saturation, value);  // Convert HSV to RGB
		[rgbR, rgbG, rgbB] = rgb;
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);  // Convert RGB to HEX
		const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);  // Convert RGB to HSL
		[hslH, hslS, hslL] = hsl;
		const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);  // Convert RGB to CMYK
		[cmykC, cmykM, cmykY, cmykK] = cmyk;
	}

	function onSatValChange() {
		const rgb = convert.hsv.rgb(hue, saturation, value);  // Convert HSV to RGB
		[rgbR, rgbG, rgbB] = rgb;
		hex = convert.rgb.hex(rgbR, rgbG, rgbB);  // Convert RGB to HEX
		const hsl = convert.rgb.hsl(rgbR, rgbG, rgbB);  // Convert RGB to HSL
		[hslH, hslS, hslL] = hsl;
		const cmyk = convert.rgb.cmyk(rgbR, rgbG, rgbB);  // Convert RGB to CMYK
		[cmykC, cmykM, cmykY, cmykK] = cmyk;
	}
	

</script>


<main>
	<div class="picker-container">
		<!-- Add elements for the hue slider and the color picker area -->
		<input class="hue-slider" type="range" min="0" max="360" bind:value={hue} on:input={onHueChange} />


	  
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<div class="saturation-value-area" style="background-color: {backgroundColor};" on:mousemove= {startDragging} >
		  	<div class="picker-indicator" style="left: {pickerX}%; top: {pickerY}%;"></div>
		</div>

		<div class="colorshow" style="height: 50px; width: 100%; background-color: {showcolor};"></div>

		<!-- Inputs for color formats -->
		<div class="color-inputs">
			<label for="HEX">hex</label>
			<input type="text" id="HEX" placeholder="HEX" bind:value ={hex} on:input={onHexInput} />
		  
			<!-- <input type="text" placeholder="RGB" bind:value ={rgbB} on:input={onRgbInput} /> -->
			<div class="nothex">
				<label for="HEX">RGB</label>
				<input type="number" placeholder="RGB" bind:value={rgbR} on:input={onRgbInput}  />
				<input type="number" placeholder="RGB" bind:value={rgbG} on:input={onRgbInput}  />
				<input type="number" placeholder="RGB" bind:value={rgbB} on:input={onRgbInput}  />
			</div>
		</div>

		<div class="color-inputs">
			<div class="nothex">
				<label for="HEX">cmyk</label>
				<input type="number" bind:value={cmykC} on:input={onCmykInput} min="0" max="100" />
  				<input type="number" bind:value={cmykM} on:input={onCmykInput} min="0" max="100" />
  				<input type="number" bind:value={cmykY} on:input={onCmykInput} min="0" max="100" />
 				<input type="number" bind:value={cmykK} on:input={onCmykInput} min="0" max="100" />
			</div>
		    
			<div class="nothex">
				<label for="HEX">hsl</label>
				<input type="number" bind:value={hslH} on:input={onHslInput} min="0" max="360" />
  				<input type="number" bind:value={hslS} on:input={onHslInput} min="0" max="100" />
  				<input type="number" bind:value={hslL} on:input={onHslInput} min="0" max="100" />
			</div>

			<div class="nothex"> 
				<label for="HEX">hsv</label>
				<input type="number" bind:value={hue} on:input={onHsvInput} min="0" max="360" />
  				<input type="number" bind:value={saturation} on:input={onHsvInput} min="0" max="100" />
  				<input type="number" bind:value={value} on:input={onHsvInput} min="0" max="100" />
			</div>
		</div>
	</div>
</main>



<style>
	/* Add basic styles for the layout */
	.picker-container {
	  display: flex;
	  flex-direction: column;
	  gap:10px;
	  width: 50%;
	  height: 100%;
	  margin: 0 auto;
	}
	.hue-slider {
	  width: 100%;
	}
	.saturation-value-area {
	  width: 100%;
	  height: 135px;
	  background: linear-gradient(to right, #fff, rgba(255, 255, 255, 0)), linear-gradient(to top, #000, rgba(0, 0, 0, 0));
	  position: relative;
      border: 1px solid grey;
      /* cursor: crosshair; */
	  border-radius: 2%;
	}


	.color-inputs {
	  display: flex;
	  justify-content: space-between;
	}
	input {
	  width: 100px;
	  padding: 5px;
	}

	.hue-slider {
    width: 100%;
    appearance: none;
    height: 12px;
    border-radius: 5px;
    background: linear-gradient(
      to right,
      red,
      yellow,
      lime,
      cyan,
      blue,
      magenta,
      red
    );
    outline: none;
    cursor: pointer;
    margin: 10px 0;
  }

  .hue-slider::-webkit-slider-thumb {
    appearance: none;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: #fff;
    border: 2px solid #000;
    cursor: pointer;
  }

  .hue-slider::-moz-range-thumb {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: #fff;
    border: 2px solid #000;
    cursor: pointer;
  }



  .picker-indicator {
    position: absolute;
    width: 12px;
    height: 12px;
    border-radius: 50%;
    border: 2px solid #fff;
    box-shadow: 0 0 3px rgba(0, 0, 0, 0.5);
    background-color: #000;
    pointer-events: none;
  }


  .nothex  {
	display: inline-block;
	width: 100%;
  }

  .nothex label, input {
	width: 50%;
  }


  </style>