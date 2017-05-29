<template>
    <div class="row my-4">
        <button class="btn btn-outline-secondary btn-block" @click="getWeatherByCoords">Get Weather for my location</button>
    </div>
</template>

<script>
export default {
    methods: {
        getWeatherByCoords: function () {
            this.$emit('getWeatherByCoords');
        }
    }
}
</script>

<style lang="scss">

</style>
