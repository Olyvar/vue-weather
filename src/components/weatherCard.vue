<template>
    <div class="card" style="width: 20rem;" v-if="dataReceived">
        <img class="card-img-top" src="http://thefw.com/files/2013/04/weknowgifs.com_.gif" alt="Card image cap">
        <div class="card-block">
            <h4 class="card-title">Day: {{ day }}</h4>
            <h4 class="card-title">Weather: {{ weather.weather[0].main }}</h4>
            <h4 class="card-title">Temperature: {{ tempDegrees }}</h4>
        </div>
    </div>
</template>

<script>
import vm from '../App.vue';
import moment from 'moment';

export default {
    props: ['weather', 'dataReceived'],
    computed: {
        tempDegrees:  function() {
            console.log(this.weather)
            return (this.weather.temp.day - 273.15).toFixed(1) + '°C'
        },
        day: function() {
            return moment(this.weather.dt).format("dddd");
        }
    }
}
</script>

<style lang="scss">

</style>
