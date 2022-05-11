<template>
    <div class="weather" :class="isDay ? 'day' : 'night'">
        <div class="weather__wrapper">
            <div class="weather__header">
                <div class="weather__title">Your city is</div>
                <form action="" v-on:submit.prevent="getWeather" class="weather__form">
                    <input type="text" class="weather__city" placeholder="Choose your city..." v-model="citySearch">
                </form>
            </div>

            <div class="weather__body">
                <div class="weather__body-inner">
                    <div class="weather__city-name">{{weather.cityName}}</div>
                    <div class="weather__country">{{weather.country}}</div>
                    <div class="weather__date">{{dateBuilder ()}}</div>

                    <div class="weather__icon">
                        <img :src="'http://openweathermap.org/img/wn/' + weather.icon">
                    </div>

                    <div class="weather__temperature">{{weather.temperature}}&deg;</div>
                    <div class="weather__desc">{{weather.description}}</div>
                    <div class="weather__temp-blocks">
                        <div class="weather__lowtemp">{{weather.lowTemp}} &deg;</div>
                        <div class="weather__hightemp">{{weather.highTemp}} &deg;</div>
                    </div>
                    <div class="weather__temp-blocks">
                        <div class="weather__feellike">
                            {{weather.feelLike}}&deg;
                            <div>feel like</div>
                        </div>
                        <div class="weather__humidity">
                            {{weather.humidity}} %
                            <div>humidity</div>
                        </div>
                    </div>
                    <div >{{citySearch}}</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'weather-main',
        data() {
            return {
                isDay: true,
                weather: {
                    cityName: '',
                    country: '',
                    temperature: '',
                    description: '',
                    lowTemp: '',
                    highTemp: '',
                    feelLike: '',
                    humidity: '',
                    icon: ''
                },
                citySearch: '',
                //city: ''
            }
        },
        mounted() {
            //this.getWeather(),
            this.getLocation()
        },
        methods: {
            async getWeather() {
                const key = import.meta.env.VITE_API_OPENWEATHERMAP;
                const baseURL  = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

                const response = await fetch(baseURL);
                const data = await response.json();
                console.log(data);

                this.citySearch = '';
                this.weather.cityName = data.name;
                this.weather.country = data.sys.country;
                this.weather.temperature = Math.round(data.main.temp);
                this.weather.description = data.weather[0].description;
                this.weather.lowTemp = Math.round(data.main.temp_min);
                this.weather.highTemp = Math.round(data.main.temp_max);
                this.weather.feelLike = Math.round(data.main.feels_like);
                this.weather.humidity = Math.round(data.main.humidity);
                this.weather.icon = data.weather[0].icon + '@2x.png';

                const timeOfDay = data.weather[0].icon;

                if(timeOfDay.includes('n')) {
                    this.isDay = false;
                } else {
                    this.isDay = true;
                }
            },
            dateBuilder () {
                let d = new Date();
                let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
                let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
                let day = days[d.getDay()];
                let date = d.getDate();
                let month = months[d.getMonth()];
                let year = d.getFullYear();

                return `${day} ${date} ${month} ${year}`;
            },
            async getLocation() {
                let api_url = 'https://ipapi.co/json/';
                let api_response = await fetch(api_url);
                let api_data = await api_response.json();
                // console.log(api_data.ip)

                let geo_url = `http://ipwho.is/${api_data.ip}`;
                let geo_response = await fetch(geo_url);
                let geo_data = await geo_response.json();
                let city = geo_data.city;
                this.citySearch = city;
                console.log(city);

                this.getWeather();              
            },
        },
        
    }
</script>

<style lang="scss" scoped>
* {
    outline: none !important;
}
.weather {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    &.night {
        background: linear-gradient(168.44deg, #2E335A 1.62%, #1C1B33 95.72%);
    }
    &.day {
        background: linear-gradient(180deg, #62B8F6 0%, #2C79C1 77.96%);
    }

    &__wrapper {
        max-width: 390px;
        flex: 0 0 390px;
        margin: 0 auto;   
    }
    &__header {
        margin-bottom: 30px;
    }
    &__title {
        font-size: 28px;
        font-weight: 500;
        text-align: center;
        color: #fff;
        margin-bottom: 10px;
    }
    &__form {
        position: relative;
        &:before {
            content: '';
            position: absolute;
            top: 50%;
            left: 15px;
            transform: translateY(-50%);
            z-index: 2;
            display: block;
            width: 20px;
            height: 20px;
            background: url(@/assets/img/search-icon.png) no-repeat;
            background-size: 20px;
        }
    }
    &__city {
        width: 100%;
        height: 40px;
        border-radius: 30px;
        border: 1px solid transparent;
        padding: 5px 10px 5px 45px;
        &:focus {
            border: 1px solid #fff;
        }
    }
    &__body {
        box-shadow: -40px 60px 150px rgba(59, 38, 123, 0.7);
        border-radius: 44px;
        padding: 30px;
    }
    &__city-name {
        font-size: 34px;
        line-height: 41px;
        letter-spacing: 0.374px;
        color: #fff;
        // margin-bottom: 10px;
        text-align: center;
    }
    &__country {
        text-align: center;
        font-size: 20px;
        color: #fff;
        font-weight: 500;
        margin-bottom: 15px;
    }
    &__date {
        text-align: center;
        color: #fff;
        font-size: 20px;
        margin-bottom: 25px;
    }
    &__temperature {
        font-weight: 100;
        font-size: 96px;
        line-height: 70px;
        letter-spacing: 0.374px;
        color: #fff;
        text-align: center;
        margin-bottom: 25px;
    }
    &__desc {
        color: rgba(235, 235, 245, 0.6);
        font-weight: 600;
        font-size: 20px;
        line-height: 24px;
        text-align: center;
        letter-spacing: 0.38px;
        margin-bottom: 15px;
    }
    &__temp-blocks {
        display: flex;
        align-items: center;
        justify-content: space-around;
        & + .weather__temp-blocks {
            margin-top: 15px;
        }
    }
    &__lowtemp,
    &__hightemp {
        display: flex;
        align-items: center;
        font-weight: 600;
        font-size: 20px;
        line-height: 24px;
        color: #fff;
        &:before {
            content: '';
            display: block;
            width: 13px;
            height: 15px;
            margin-right: 5px;
            background: url(@/assets/img/down-arrow.png) no-repeat;
            background-size: 16px;
        }
    }
    &__hightemp:before {
        background: url(@/assets/img/up-arrow.png) no-repeat;
        background-size: 16px;
    }
    &__humidity,
    &__feellike {
        font-size: 35px;
        text-align: center;
        color: #fff;
        font-weight: 300;
        & > div {
            font-size: 16px
        }
    }
    &__icon {
        text-align: center;
        margin-bottom: 25px;
    }
    
}

</style>