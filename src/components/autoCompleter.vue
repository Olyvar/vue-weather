<template>
    <div class="row">
        <input type="text" class="form-control" v-model="query" v-on:keyup="initAutocomplete($event)" v-on:keyup.up="upArrowScroll($event)" v-on:keyup.down="downArrowScroll($event)">
        <ul class="list-unstyled autocomplete-list">
            <li class="autocomplete-list--item" v-for="(place, $index) in places" :key="place.id" v-bind:class="{'autocomplete-list--item__active':place.selected}" @click="selectPlace(place)" v-on:mouseover="resyncAutocompleteIdx($index)">
                {{ place.name }}, {{ place.container }}
            </li>
        </ul>
        <button class="btn btn-outline-primary btn-block mb-5">Get Weather for input</button>
    </div>
</template>

<script>
import vm from '../App.vue';
import axios from 'axios';

export default {
    data: function(){
        return {
            APIKEY: 'YB0MY3VMHyllzPqEf5alVj5bUvGpvDVi',
            UPARROW: 38,
            DOWNARROW: 40,
            query: '',
            places: [],
            selectedPlaceIdx: 0
        }
    },
    methods: {
        resyncAutocompleteIdx: function($index){
            Object.assign(this.places[this.selectedPlaceIdx], {selected:false});
            this.selectedPlaceIdx = $index;
            Object.assign(this.places[this.selectedPlaceIdx], {selected:true});
        },
        resetSelectedPlace: function(){
            let resetPlace = Object.assign(this.places[this.selectedPlaceIdx], {selected:false});
            Vue.set(this.places, this.selectedPlaceIdx, resetPlace)
        },
        initAutocomplete: function ($event) {
            if(this.query.length <= 1) return;
            if($event.which === 38 || $event.which === 40) return
            axios.get(`http://data.bbc.co.uk/locservices-locator/locations?apikey=${this.APIKEY}&vv=2&format=json&order=importance&filter=international&place-types=settlement&auto=true&search=${this.query}`)
                .then(raw => {
                    if(raw.data.response.content.locations.locations.length === 1) this.places = [];
                    this.places = raw.data.response.content.locations.locations;
                })
        },
        upArrowScroll: function($event){
            this.resetSelectedPlace();
            this.selectedPlaceIdx--;
            if(this.selectedPlaceIdx === -1) this.selectedPlaceIdx = 9;
            this.resetQuery($event);
        },
        downArrowScroll: function($event){
            this.resetSelectedPlace();
            this.selectedPlaceIdx++;
            if(this.selectedPlaceIdx === 10) this.selectedPlaceIdx = 0;
            this.resetQuery($event);
        },
        resetQuery: function($event){
            let newObj = Object.assign(this.places[this.selectedPlaceIdx], {selected:true});
            this.query = `${this.places[this.selectedPlaceIdx].name}, ${this.places[this.selectedPlaceIdx].container}`;
            Vue.set(this.places, this.selectedPlaceIdx, newObj)
        },
        selectPlace: function(place){
            this.query = `${place.name}, ${place.container}`;
            this.places = [];
            vm.loading = true;

            axios.get(`http://api.openweathermap.org/data/2.5/weather?q=${place.name}&APPID=${vm.APIKEY}`)
                .then(raw => {
                    vm.weather.city = raw.data.name;
                    vm.weather.main = raw.data.weather[0].main;
                    vm.weather.temp = raw.data.main.temp;

                    vm.loading = false;
                    vm.gotWeather = true;
                })
        }
    }
}
</script>

<style lang="scss">
    .autocomplete-list{
        width: 100%;
    }

    .autocomplete-list--item{
        padding: .5em;
        margin-top: -2px;
        border: 1px solid rgba(255,255,255,0.3);
        color: white;
        width: 100%;
        display: inline-block;
        text-align: left;
    }

    .autocomplete-list--item:hover, .autocomplete-list--item__active{
        border-color: rgba(255,255,255,1);
    }
</style>
