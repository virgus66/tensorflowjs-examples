<template>
	<div>
		<h1>Multiple object detection using pre trained model in TensorFlow.js</h1>
		<span>COCO SSD = Common Objects in Contex - Single Shot Detector</span>
		<div class="mt-10">
			<div class="mb-4">FPS: {{ fps.toFixed(2) }}</div>
			<div 
				v-if="!cocoSsdLoaded"
				class="font-bold animation1"
			>
				Loading Coco Ssd...
			</div>
			<div
				ref="liveView"
				:class="[{ 'invisible': !cocoSsdLoaded }, 'camView']"
			>
				<button
					v-if="!camEnabled"
					id="webcamButton"
					ref="webcamButton"
				>
					Enable Webcam
				</button>
				<video
					ref="webcam"
					autoplay
					muted
					width="1280"
					height="720"
				></video>
				<PredictionBox
					v-for="prediction in predictions"
					:key="prediction.score"
					:prediction="prediction"
				/>
			</div>
		</div>
	</div>
</template>

<script>
import "@tensorflow/tfjs"
import * as cocoSsd from "@tensorflow-models/coco-ssd"
import PredictionBox from "../components/PredictionBox.vue"

export default {
	name: 'Example2',
	components: { PredictionBox },
	data: () => ({
		model: null,
		predictions: [],
		cocoSsdLoaded: false,
		camEnabled: false,
		fpsCounter: 0,
		fps: 0,
		lastLoop: new Date()
	}),
	mounted() {
		this.init()
	},
	methods: {
		async init() {
			if (this.getUserMediaSupported) {
				this.$refs.webcamButton.addEventListener('click', this.enableCam)
			} else console.warn('getUserMedia() is not supported by your browser')

			const loadedModel = await cocoSsd.load()
			this.model = loadedModel
			this.cocoSsdLoaded = true
		},
		getUserMediaSupported() {
			return !!(navigator.mediaDevices && navigator.mediaDevices.getUserMedia)
		},
		enableCam() {
			if (!this.model) return
			this.camEnabled = true
			navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
				this.$refs.webcam.srcObject = stream
				this.$refs.webcam.addEventListener('loadeddata', () => {
					this.predictWebcam()
					setInterval(() => this.fps = this.fpsCounter, 200)
				})
			});
		},
		async predictWebcam() {
			this.predictions = await this.model.detect(this.$refs.webcam)
			console.log('predictions', this.predictions)
			
			window.requestAnimationFrame(this.predictWebcam)
			this.getFPS()
		},
		getFPS() { 
			const thisLoop = new Date()
			this.fpsCounter = 1000 / (thisLoop - this.lastLoop)
			this.lastLoop = thisLoop
		}
	}
}
</script>

<style>
.animation1 {
	animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}
@keyframes pulse {
	0%, 100% {
		opacity: 1;
		transform: translateY(-25%);
		animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
	}
	50% {
		opacity: .1;
		transform: translateY(0);
		animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
	}
}
body {
	font-family: helvetica, arial, sans-serif;
	margin: 2em;
	color: #3D3D3D;
}
h1 {
	font-size: 2rem;
	/* font-style: italic; */
	color: #FF6F00;
}
video {
	display: block;
}
section {
	opacity: 1;
	transition: opacity 500ms ease-in-out;
}
.invisible {
	opacity: 0.2;
}
.camView {
	position: relative;
	float: left;
	width: calc(100% - 20px);
	cursor: pointer;
}
#webcamButton {
	background-color: #cf245f;
	background-image: linear-gradient(to bottom right, #fcd34d, #ef4444, #ec4899);
	border: 0;
	border-radius: .25rem;
	box-sizing: border-box;
	color: #fff;
	cursor: pointer;
	font-family: ui-sans-serif,system-ui,-apple-system,system-ui,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji";
	font-size: 1.125rem;
	font-weight: 600;
	line-height: 1.75rem;
	padding: 1rem 1.25rem;
	text-align: center;
	user-select: none;
	-webkit-user-select: none;
	touch-action: manipulation;
}
</style>
