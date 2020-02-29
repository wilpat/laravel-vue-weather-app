<template>
    <div class="text-white mb-8">
        <div class="places-input text-gray-800">
            <input type="text" class="w-full">
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
                    <canvas id="icon1" width="96" height="96"></canvas>
                </div>
            </div> 
            <!-- End Current weather section -->
            <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
                <div class="flex items-center">
                    <div class="w-1/6 text-lg text-gray-200">MON</div>
                    <div class="w-4/6 px-4 flex items-center">
                        <div>Icon</div>
                        <div class="ml-3">Cloudy with a chance of meatballs</div>
                    </div>
                    <div class="text-right">
                        <div class="high">5&deg;C</div>
                        <div class="low">-6&deg;C</div>
                    </div>
                </div>

                <div class="flex items-center mt-8">
                    <div class="w-1/6 text-lg text-gray-200">MON</div>
                    <div class="w-4/6 px-4 flex items-center">
                        <div>Icon</div>
                        <div class="ml-3">Cloudy with a chance of meatballs</div>
                    </div>
                    <div class="text-right">
                        <div class="high">5&deg;C</div>
                        <div class="low">-6&deg;C</div>
                    </div>
                </div>

                <div class="flex items-center mt-8">
                    <div class="w-1/6 text-lg text-gray-200">MON</div>
                    <div class="w-4/6 px-4 flex items-center">
                        <div>Icon</div>
                        <div class="ml-3">Cloudy with a chance of meatballs</div>
                    </div>
                    <div class="text-right">
                        <div class="high">5&deg;C</div>
                        <div class="low">-6&deg;C</div>
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
        },
        data() {
            return {
                location: {
                    name: 'Toronto, Canada',
                    lat: '43.6532',
                    lng: '-79.38323'
                },
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
                    this.currentTemp.actual = Math.round(data.currently.temperature);
                    this.currentTemp.feels =Math.round(data.currently.apparentTemperature);
                    this.currentTemp.summary = data.currently.summary;
                    this.currentTemp.icon = this.toKebab(data.currently.icon);
                    skycons.add('icon1', this.currentTemp.icon);
                    skycons.play();
                })
            },
            toKebab(string) {
                return string.split(' ').join('-');
            }
        }
    }
</script>
