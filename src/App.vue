<template>
<div class="container">

  <auto-completer></auto-completer>

  <weather-grabber v-on:getWeatherByCoords="getWeather"></weather-grabber>

  <p v-if="geolocationDenied">
    Please accept location tracking
  </p>

  <div v-if="loading">
    <div class="loader loader--style1">
      <svg version="1.1" id="loader-1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
 width="40px" height="40px" viewBox="0 0 40 40" enable-background="new 0 0 40 40" xml:space="preserve">
<path opacity="0.2" fill="#000" d="M20.201,5.169c-8.254,0-14.946,6.692-14.946,14.946c0,8.255,6.692,14.946,14.946,14.946
  s14.946-6.691,14.946-14.946C35.146,11.861,28.455,5.169,20.201,5.169z M20.201,31.749c-6.425,0-11.634-5.208-11.634-11.634
  c0-6.425,5.209-11.634,11.634-11.634c6.425,0,11.633,5.209,11.633,11.634C31.834,26.541,26.626,31.749,20.201,31.749z"/>
<path fill="#000" d="M26.013,10.047l1.654-2.866c-2.198-1.272-4.743-2.012-7.466-2.012h0v3.312h0
  C22.32,8.481,24.301,9.057,26.013,10.047z">
  <animateTransform attributeType="xml"
    attributeName="transform"
    type="rotate"
    from="0 20 20"
    to="360 20 20"
    dur="0.5s"
    repeatCount="indefinite"/>
  </path>
</svg>
    </div>
  </div>

  <transition name="fade">
    <weather-card :weather="weather"></weather-card>
  </transition>

</div>
</template>

<script>
import axios from 'axios';

import weatherGrabber from './components/weatherGrabber.vue';
import weatherCard from './components/weatherCard.vue';
import autoCompleter from './components/autoCompleter.vue';

export default {
    components: {
        'weather-grabber': weatherGrabber,
        'auto-completer': autoCompleter,
        'weather-card': weatherCard
    },
    data: function(){
        return {
            APIKEY: '2303473530dc8817a6b6bd8fc53a9b13',
            geolocationDenied: false,
            coords: {
                lat: null,
                lon: null
            },
            loading: false,
            weather: {
                city: '',
                main: '',
                temp: '',
                dataReceived: false
            }
        }
    },
    methods: {
        getWeatherByCoords: function () {

            this.loading = true;

            const success = function (pos) {
                this.coords.lat = pos.coords.latitude;
                this.coords.lon = pos.coords.longitude;

                axios
                    .get(
                        `http://api.openweathermap.org/data/2.5/weather?lat=${this.coords.lat}&lon=${this.coords.lon}&APPID=${this.APIKEY}`
                    )
                    .then( (raw) => {
                        this.weather.city = raw.data.name;
                        this.weather.main = raw.data.weather[0].main;
                        this.weather.temp = raw.data.main.temp;

                        this.loading = false;
                        this.weather.dataReceived = true;
                    })
            };

            const error = function () {
                this.loading = false;
                this.geolocationDenied = true;
            };

            navigator.geolocation.getCurrentPosition(success.bind(this), error.bind(this));

        },
        getWeather: function(){
            ("geolocation" in navigator) ? this.getWeatherByCoords() : '';
        }
    }
}
</script>

<style lang="scss">
  .fade-enter-active, .fade-leave-active {
    transition: opacity 1s
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
    opacity: 0
  }

  .btn:hover{
    cursor: pointer;
  }

  body{
    padding: 1em;
    background: #2B3134;
    color: #777;
    text-align: center;
    font-family: "Gill sans", sans-serif;
    width: 80%;
    margin: 0 auto;
  }
  h1{
    margin: 1em 0;
    border-bottom: 1px dashed;
    padding-bottom: 1em;
    font-weight: lighter;
  }
  p{
    font-style: italic;
  }
  .loader{
    margin: 0 0 2em;
    height: 100px;
    width: 20%;
    text-align: center;
    padding: 1em;
    margin: 0 auto 1em;
    display: inline-block;
    vertical-align: top;
  }

  /*
    Set the color of the icon
  */
  svg path,
  svg rect{
    fill: #FF6700;
  }


</style>
