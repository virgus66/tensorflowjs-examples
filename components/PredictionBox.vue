<template>
<div>
	<p :style="'margin-left: ' + prediction.bbox[0] + 'px; margin-top: '
							+ (prediction.bbox[1] - 30) + 'px; width: ' 
							+ (prediction.bbox[2]) + 'px; top: 0; left: 0;'"
	>
		{{ `${prediction.class} - with ${Math.round(parseFloat(prediction.score) * 100)} % confidence.` }}
	</p>
	<div 
		class="highlighter"
		:style="'left: ' + prediction.bbox[0] + 'px; top: '
			+ prediction.bbox[1] + 'px; width: ' 
			+ prediction.bbox[2] + 'px; height: '
			+ prediction.bbox[3] + 'px;'"
	>
	</div>
</div>
</template>

<script>
export default {
	name: 'PredictionBox',
	props: {
		prediction: { type: Object }
	}
}
</script>

<style scoped>
p {
	position: absolute;
	padding: 5px;
	background-color: rgba(255, 111, 0, 0.85);
	color: #FFF;
	border: 1px dashed rgba(255, 255, 255, 0.7);
	z-index: 2;
	font-size: 12px;
	opacity: 1;
	transition: opacity 500ms ease-in-out;
}
.highlighter {
	background: rgba(0, 255, 0, 0.25);
	border: 1px dashed #fff;
	z-index: 1;
	position: absolute;
}
</style>
