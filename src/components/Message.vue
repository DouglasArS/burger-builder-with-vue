<template>
	<div class="message-container">
		<p>{{ message }}</p>
	</div>
</template>

<script>
	export default {
		name: "Message",
		props: {
			message: String
		}
	}
</script>

<style scoped>
	.message-container {
		color: #004085;
		background-color: #CCE5FF;
		border: 2px solid #B8DAFF;
		border-radius: 5px;
		padding: 5px;
		max-width: 400px;
		margin: 30px auto;
	}
</style>
