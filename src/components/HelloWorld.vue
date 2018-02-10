<template>
	<div class="hello">
		<h1>Vue Midi abcjs Demo</h1>
		<p>This is a simple app that just displays an audio control.</p>
		<p>The only addition to the standard vue-cli app's dependencies is:</p>
		<pre>npm install abcjs --save-dev
npm install font-awesome --save</pre>
		<p>The only necessary code to make this work is:</p>
		<pre>import "font-awesome/css/font-awesome.min.css";
import 'abcjs/abcjs-midi.css';
import abcjs from "abcjs/midi";

new abcjs.Editor("abc-source", {
	canvas_id: "paper",
	generate_midi: true,
	midi_id: "midi"
});</pre>
		<pre>&lt;textarea id="abc-source"&gt;&lt;/textarea&gt;
&lt;div id="midi"&gt;&lt;/div&gt;
&lt;div id="paper"&gt;&lt;/div&gt;</pre>
		<p>In addition, this demo shows how to use the callbacks provided by the MIDI controls. See the file "HelloWorld.vue"
			to see how to use that.
		</p>
		<textarea id="abc-source">{{tune}}</textarea>
		<div class="listener-output">
			<div class="label">Parameters sent to listener callback: </div>
			<div>Progress: {{progress.progress }}</div>
			<div>Current Time: {{progress.currentTime }}</div>
			<div>Total Duration: {{progress.duration }}</div>
			<div>New Beat? {{progress.newBeat }}</div>
		</div>
		<div id="midi"></div>
		<div id="paper"></div>
	</div>
</template>

<script>
	import "font-awesome/css/font-awesome.min.css";
	import 'abcjs/abcjs-midi.css';
	import abcjs from "abcjs/midi";

	export default {
		mounted: function () {
			new abcjs.Editor("abc-source", {
				canvas_id: "paper",
				generate_midi: true,
				midi_id: "midi",
				midi_options: {
					listener: this.listener,
					animate: {
						listener: this.animate,
					}
				}
			});
			},
		name: 'hello',
		data () {
			return {
				progress: { },
				tune: 'X:1\nT: Cooley\'s\nM: 4/4\nL: 1/8\nR: reel\nK: Emin\nD2|:"Em"EB{c}BA B2 EB|~B2 AB dBAG|"D"FDAD BDAD|FDAD dAFD|\n"Em"EBBA B2 EB|B2 AB defg|"D"afe^c dBAF|1"Em"DEFD E2 D2:|2"Em"DEFD E2 gf||\n|:"Em"eB B2 efge|eB B2 gedB|"D"A2 FA DAFA|A2 FA defg|\n"Em"eB B2 eBgB|eB B2 defg|"D"afe^c dBAF|1"Em"DEFD E2 gf:|2"Em"DEFD E4|]\n'
			};
		},
		methods: {
			listener(midiControl,progress) {
				// This provides a more linear view of the progress, for a progress bar or for an unrelated animation.
				this.progress = progress;
			},
			colorRange(range, color) {
				if (range && range.elements) {
					range.elements.forEach(function (set) {
						set.forEach(function (item) {
							item.attr({fill: color});
						});
					});
				}
			},
			animate(lastRange, currentRange) {
				// This provides the actual visual note being played. It can be used to create the "bouncing ball" effect.
				this.colorRange(lastRange, "#000000"); // Set the old note back to black.
				this.colorRange(currentRange, "#3D9AFC"); // Set the currently sounding note to blue.
			},
		}
	}
</script>

<style>
	.hello {
		text-align: left;
	}
	#abc-source {
		width: 460px;
		height: 160px;
		padding: 6px;
	}

	.listener-output {
		border: 1px solid #888888;
		padding: 6px;
		border-radius: 4px;
		width: 460px;
		margin-bottom: 20px;
	}

	pre {
		border: 1px solid #888888;
		padding: 6px;
		border-radius: 4px;
		width: 460px;
	}

	#midi {
		width: 756px;
	}

	.label {
		font-weight: bold;
	}

</style>
