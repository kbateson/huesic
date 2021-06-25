<template>
    <div>
        Red: <RangeSlider
                class="slider"
                min="0"
                max="255"
                v-model="red"
                v-on:input="colorChange()" /><br />
        Green: <RangeSlider
                class="slider"
                min="0"
                max="255"
                v-model="green"
                v-on:input="colorChange()" /><br />
        Blue: <RangeSlider
                class="slider"
                min="0"
                max="255"
                v-model="blue"
                v-on:input="colorChange()" />
    </div>
</template>

<script>
import RangeSlider from 'vue-range-slider'
import 'vue-range-slider/dist/vue-range-slider.css'

export default {
    components: {
        RangeSlider
    },
    data() {
        return {
            red: 255,
            green: 255,
            blue: 255
        }
    },
    methods: {
        colorChange(color) {
            if(color) {
                this.red = color.red;
                this.green = color.green;
                this.blue = color.blue;
            }
            this.$emit('colorChange', {
                red: this.red,
                green: this.green,
                blue: this.blue
            })
        }
    }
}
</script>