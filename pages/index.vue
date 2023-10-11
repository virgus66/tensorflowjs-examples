<template>
<div class="p-5">
	<div class="mb-10">
		<p class="text-center text-4xl"><strong>2x - 1</strong></p>
		<p>Expected output for x = 5 -> <strong>{{ 2 * 5 - 1}}</strong></p>
	</div>
	<div
		v-for="result in results"
		class="mb-4 border p-4"
	>
		<p>Epochs: {{ result.epochs }}</p>
		<p>Prediction: <span class="font-bold">{{ result.prediction }}</span></p>
		<p>Execution time: {{ (result.executionTime / 1000).toFixed(2) }}s</p>
		<p>Epochs/s: {{ (result.epochs / (result.executionTime / 1000)).toFixed(2) }}</p>
	</div>
</div>
</template>

<script>
import * as tf from '@tensorflow/tfjs'

export default {
	name: 'Example1',
	data: () => ({
		results: []
	}),
	async created() {
		await this.createAndRunModel(250);
		await this.createAndRunModel(500);
		await this.createAndRunModel(1500);
		await this.createAndRunModel(3500);
		await this.createAndRunModel(10000);
		await this.createAndRunModel(15000);
	},
	methods: {
		async createAndRunModel(epochs) {
			const startTime = performance.now()

			const model = tf.sequential()

			model.add(tf.layers.dense({
				units: 1, // Positive integer, dimensionality of the output space
				inputShape: [1] // If defined, will be used to create an input layer to insert before this layer
			}))

			// Configures and prepares the model for training and evaluation
			model.compile({
				loss: 'meanSquaredError', // Function to help optimize the performance of the model, a single number for "how wrong" the models prediction was
				optimizer: 'sgd' || 'sgd' // An algorithm used to minimize a loss function, sgd | adam
			})

			const xs = tf.tensor2d(
				[1, 2, 3, 4], // Input data
				[4, 1]  // Shape and dimension of tensor
			)
			const ys = tf.tensor2d(
				[1, 3, 5, 7], // Labels
				[4, 1] // Shape and dimension of tensor
			)

			await model.fit(xs, ys, { epochs })

			const newTensor = tf.tensor2d([5], [1, 1])
			const prediction = model
				.predict(newTensor)
				.dataSync()

			const endTime = performance.now()

			this.results.push({
				prediction,
				epochs,
				executionTime: endTime - startTime
			})
		}
	}
}
</script>



// Generate some synthetic data for training. (y = 2x - 1)
// const xs = tf.tensor2d(
//   [-1, 0, 1, 2, 3, 4],
//   [6, 1]
// )
// const ys = tf.tensor2d(
//   [-3, -1, 1, 3, 5, 7],
//   [6, 1]
// )