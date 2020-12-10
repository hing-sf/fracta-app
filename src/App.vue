<template>
	<div id="app">
		<PipelineContainer msg="Map Component" />
	</div>
</template>

<script>
import PipelineContainer from './components/pipeline/PipelineContainer.vue';

export default {
	name: 'App',
	components: {
		PipelineContainer
	}
};
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
