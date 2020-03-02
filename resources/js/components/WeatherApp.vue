<template>
    <div class="text-white mb-8">
        <div class="places-input text-gray-800">
             <input type="search" id="country" class="form-control text-gray-800" placeholder="Enter a country or city" />
        </div>
        
        <div class="weather-container font-sans w-128 max-w-lg overflow-hidden bg-gray-900 shadow-lg mt-4 rounded-lg">
            <div class="current-weather flex items-center justify-between px-6 py-8">
                <div class="flex items-center top-first">
                    <div class="left-first">
                        <div class="text-6xl font-semibold">{{currentTemp.actual}}&deg;C</div>
                        <div>Feels like {{currentTemp.feels}}&deg;C</div>
                    </div>
                    <div class="left-second mx-5">
                        <div class="font-semibold">{{currentTemp.summary}}</div>
                        <div>{{location.name}}</div>
                    </div>
                </div>
                <div class="top-second">
                    <canvas id="icon-today" width="96" height="96"></canvas>
                </div>
            </div> 
            <!-- End Current weather section -->
            <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
                <div class="flex items-center" 
                     :class="{'mt-8' : index > 0}"
                     v-for="(day, index) in computedDaily" 
                     :key="index">
                    <div class="w-1/6 text-lg text-gray-200">{{ toDayOfWeek(day.time) }}</div>
                    <div class="w-4/6 px-4 flex items-center">
                        <div><canvas :id="`icon${index+1}`" width="24" height="24" :data-icon="day.icon"></canvas></div>
                        <div class="ml-3">{{ day.summary }}</div>
                    </div>
                    <div class="text-right">
                        <div class="high">{{ Math.round(day.temperatureHigh) }}&deg;C</div>
                        <div class="low">{{ Math.round(day.temperatureLow) }}&deg;C</div>
                    </div>
                </div>
            </div>
            <!-- End future weather -->

        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            this.fetchData();
            this.loadInput();
        },
        watch: {
            location:  {
                handler(newVal, oldVal) {
                    this.fetchData();
                },
                deep: true
            }
        },
        data() {
            return {
                location: {
                    name: 'Lagos, Nigeria',
                    lat: '6.455',
                    lng: '3.3941'
                },
                daily:[],
                currentTemp: {
                    actual: '',
                    feels: '',
                    summary: '',
                    icon: ''
                }
            }
        },
        methods: {
            fetchData() {
                var skycons = new Skycons({"color": "white"});
                fetch(`/api/weather?lat=${this.location.lat}&lng=${this.location.lng}`)
                .then(response => response.json())
                .then(data => {
                    // console.log(data)
                    this.daily = data.daily.data;
                    // console.log(this.daily)
                    this.currentTemp.actual = Math.round(data.currently.temperature);
                    this.currentTemp.feels =Math.round(data.currently.apparentTemperature);
                    this.currentTemp.summary = data.currently.summary;
                    this.currentTemp.icon = this.toKebab(data.currently.icon);
                    this.$nextTick(() => {
                        skycons.add('icon-today', this.currentTemp.icon);
                        skycons.add('icon1', document.getElementById('icon1').getAttribute('data-icon'));
                        skycons.add('icon2', document.getElementById('icon2').getAttribute('data-icon'));
                        skycons.add('icon3', document.getElementById('icon3').getAttribute('data-icon'));
                        skycons.add('icon4', document.getElementById('icon4').getAttribute('data-icon'));
                        skycons.add('icon5', document.getElementById('icon5').getAttribute('data-icon'));
                        skycons.play();

                    })
                })
            },
            toKebab(string) {
                return string.split(' ').join('-');
            },
            toDayOfWeek(timestamp) {
                let time = new Date(timestamp * 1000);
                let days = ['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN'];
                return days[time.getDay()];
            },
            loadInput(){
                var placesAutocomplete = places({
                    appId: 'plBM4GROG14L',
                    apiKey: '8f8dd2d6f2e35e1a18c1bf02c467efe0',
                    container: document.querySelector('#country'),
                    templates: {
                    suggestion: function(suggestion) {
                        // console.log(suggestion)
                        return suggestion.value;
                    }
                    }
                }).configure({
                    type: 'city',
                });
                placesAutocomplete.on('change', (e) => {
                    this.location.name = `${e.suggestion.name}, ${e.suggestion.country}`;
                    this.location.lat = e.suggestion.latlng.lat;
                    this.location.lng = e.suggestion.latlng.lng;
                })
            }
        },
        computed: {
            computedDaily(){
                return this.daily.slice(0,5);
            }
        }
    }
</script>
