<template>
	<div id="weather-forecast" class="container-fluid mt-3 p-3" v-if="weatherCache !== null">
		<div class="row">
			<div class="col-6 col-md-4 col-lg-2"
					v-for="day in weatherCache.daily.slice(1,7)" :key="day.dt">
					<div class="d-flex flex-column align-items-center my-3 m-lg-0">
						<p class="day mb-0">{{ day.dt | getDayFromDT }}</p>
						<p class="date">{{ day.dt | getDateFromDT }}</p>
						<div class="icon-small mx-auto mb-2"
								:class=" day | getWeatherClass"
						></div>
						<p class="font-weight-bold mb-0"> 
							{{Math.round(day.temp.max)}}  /
							{{Math.round(day.temp.min)}} 
						</p>
					</div>
				</div>
			</div>
	</div>
</template>

<script>
import store from '@/store.js';
import moment from 'moment';
	export default {
		name: "FiveDayForecast",
		computed:{
      weatherCache:() => store.getters['getWeatherCache'],
		},

		filters:{
			getDayFromDT(dt){
				let day = moment.unix(dt).format("ddd");
				return day
			},
			getDateFromDT(dt){
				let date = moment.unix(dt).format("DD MMM");
				return date
			},
			getWeatherClass(dayData){   
				let icon = dayData.weather[0]["icon"]   
        let weatherIconMap = {
          "01d" : "icon-sunny",
          "02d" : "icon-cloudy-sun",
          "03d" : "icon-cloudy",
          "04d" : "icon-cloudy",
          "09d" : "icon-showers",
          "10d" : "icon-showers-sun",
          "11d" : "icon-storm",
          "13d" : "icon-snow",
          "50d" : "icon-fog",
          "01n" : "icon-moon",
          "02n" : "icon-cloudy-moon",
          "03n" : "icon-cloudy",
          "04n" : "icon-cloudy",
          "09n" : "icon-showers",
          "10n" : "icon-showers-moon",
          "11n" : "icon-storm",
          "13n" : "icon-snow",
          "50n" : "icon-fog",
        }
        let result = weatherIconMap[icon];
        if(!weatherIconMap[icon]){
          result = "no-icon"
        }
        return result
      },
		}
	}

</script>