<template>
	<div id="app">
		<div class="container">
			<InputSlider
				:value="range"
				:isLazy="true"
				@change="value => (range = value)"
			/>
		</div>
	</div>
</template>

<script>
import InputSlider from '@/components/InputSlider'

export default {
	name: 'App',
	components: {
		InputSlider
	},
	data() {
		return {
			range: 0
		}
	}
}
</script>

<style lang="scss">
#app {
	padding: 20px 0;
}

.container {
	max-width: 1140px;
	margin: 0 auto;
	padding: 0 30px;
}
</style>
