<template>
    <div class="row" v-if="weather.dataReceived">
        <div class="card" style="width: 20rem;">
            <img class="card-img-top" src="http://thefw.com/files/2013/04/weknowgifs.com_.gif" alt="Card image cap">
            <div class="card-block">
                <h4 class="card-title">Weather: {{ weather.main }}</h4>
                <h4 class="card-title">Temperature: {{ tempDegrees }}</h4>
                <h4 class="card-title">City: {{ weather.city }}</h4>
            </div>
        </div>
    </div>
</template>

<script>
import vm from '../App.vue';

export default {
    props: ['weather'],
    computed: {
        tempDegrees:  function() {
            return (this.weather.temp - 273.15).toFixed(1) + '°C'
        }
    }
}
</script>

<style lang="scss">

</style>
