<template>
	<div>
		<input type="text" :value="range" class="input-text" @input="changeRange" />

		<VueSlider
			v-model="range"
			:data="rangeValues"
			:lazy="isLazy"
			class="slider"
			@change="clearNonNativeMarks"
		></VueSlider>
	</div>
</template>

<script>
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/default.css'

export default {
	name: 'InputSlider',
	components: {
		VueSlider
	},
	props: {
		value: {
			type: Number,
			required: true
		},
		rangeMaximum: {
			type: Number,
			default: 10000000
		},
		isLazy: {
			type: Boolean,
			default: false
		}
	},
	data() {
		return {
			rangeObjects: [{ value: 0, native: true }],
			timer: null
		}
	},
	computed: {
		range: {
			get() {
				return this.value
			},
			set(value) {
				this.$emit('change', value)
			}
		},
		rangeValues() {
			return this.rangeObjects.map(obj => obj.value)
		}
	},
	watch: {
		rangeMaximum() {
			this.initializeRangeObjects(this.rangeMaximum)
		}
	},
	created() {
		this.initializeRangeObjects(this.rangeMaximum)
	},
	methods: {
		getDivider(number) {
			if (number >= 0 && number < 100000) {
				return 10000
			} else if (number >= 100000 && number < 200000) {
				return 50000
			} else if (number >= 200000 && number < 1000000) {
				return 100000
			} else if (number >= 1000000 && number < 10000000) {
				return 1000000
			} else {
				return 10000000
			}
		},
		initializeRangeObjects(max, initial = 10000) {
			let temp = initial

			do {
				this.rangeObjects.push({ value: temp, native: true })
				const divider = this.getDivider(temp)

				if (temp + divider > max && temp !== max) {
					temp += max - temp
				} else {
					temp += divider
				}
			} while (temp <= max)
		},
		changeRange(e) {
			let input = +e.target.value

			if (input > this.rangeMaximum) {
				input = this.rangeMaximum
				e.target.value = input
			}

			if (this.timer) {
				clearTimeout(this.timer)
				this.timer = null
			}

			this.timer = setTimeout(() => {
				if (!this.rangeValues.includes(input)) {
					this.clearNonNativeMarks()
					this.addRangeMark(input)
				}

				this.range = input
			}, 500)
		},
		addRangeMark(range) {
			for (let i = 0; i < this.rangeObjects.length; i++) {
				if (this.rangeObjects[i].value > range) {
					this.rangeObjects.splice(i, 0, { value: range, native: false })
					break
				}
			}
		},
		clearNonNativeMarks() {
			this.rangeObjects = this.rangeObjects.filter(obj => obj.native)
		}
	}
}
</script>

<style lang="scss" scoped>
.slider {
	width: 400px !important;
}
</style>
