<template>
	<div id="weather-widget" class="container-fluid h-100 ">
		<div class="row align-items-center h-100">
			<div class="col-lg-6 offset-lg-3 col-sm-8 offset-sm-2 col-12">
				<div class="row no-gutters">
					<div class="col-12">
						<input 
							v-model="enteredCity"
							@keyup.enter="getCitiesArray"
							class="search-input"
							type="text" 
							placeholder="Enter your city..."/>
						<button
								type="submit"
								@click.prevent="getCitiesArray"
								class="search-btn text-uppercase font-weight-bold">
							<i class="fa fa-search" aria-hidden="true"></i>
						</button>

						<div v-show="chosenCity"
							class="chosen-city">
								<div>
									{{getChosenCity().city }} / {{getChosenCity().country }}
								</div>
								<button>Save changes</button>
						</div>
						<div class="matches-city-list">
							<ul 	
								class="city-variants" 
								v-for="city in matchesCities" 
								:key="city.id"> 
								<li>
									{{city.name}} / {{city.country}}
									<button @click.prevent="chooseCity(city)">Choose</button>
								</li>
							</ul>
						</div>
						
					</div>
				</div>
			</div>
		</div>
	</div>   
</template>

<script>
import axios from 'axios';

	export default {
		name: "SettingsPage",
		data(){
			return{
				enteredCity: "",
				matchesCities: [],
				chosenCity: null,
			}
		},
		mounted() {
			if (localStorage.getItem('chosenCity')) {
				try {
					this.chosenCity = JSON.parse(localStorage.getItem('chosenCity'));
				}catch(error) {
					localStorage.removeItem('chosenCity');
				}
			}
		},
		methods:{
			async getCitiesArray(){
				if(!this.enteredCity) return;
				try{
					await axios.get("/city.list.min.json")
					.then((response)=>{
						let citiesArray = response.data;
						this.matchesCities = citiesArray.filter(
							city => city.name.toLowerCase().startsWith(this.enteredCity.toLowerCase())
						);
						console.log(this.matchesCities)
						if(this.matchesCities.length === 0) {
							throw new Error("Array is empty!")
						}
					})
				} catch(error) {
						console.log(error)
					} 
			},

			chooseCity(city){
				this.chosenCity= city;
				this.saveChosenCity();
			},

			saveChosenCity(){
				const parsed = JSON.stringify(this.chosenCity);
				localStorage.setItem('chosenCity', parsed);
			},

			getChosenCity(){
				let result = JSON.parse(localStorage.getItem('chosenCity'));
				if(result === null){
					return {
						city: "No city selected",
						country: ""
					}
				}
				return {
					city:result.name, 
					country: result.country
				}
			}
		},
	}

						// for(let city of response.data){
						// 	if(city.name.toLowerCase() === this.enteredCity.toLowerCase()){
						// 		console.log(this.enteredCity)
						// 		let lat = city.coord.lat;
						// 		let lon = city.coord.lon;
						// 		this.cityCoordinates.lat = lat;
						// 		this.cityCoordinates.lon = lon;
						// 		break
						// 	}
						// }
					// 	return axios({
					// 		method: 'get',
					// 		url: "https://api.openweathermap.org/data/2.5/onecall",
					// 		params:{
					// 			lat: this.cityCoordinates.lat,
					// 			lon: this.cityCoordinates.lon,
					// 			units: "metric",
					// 		appid: "4442e1e742eb4c424c00335c7acb087e",
					// 		}
					// 	})
					// })
	
</script>