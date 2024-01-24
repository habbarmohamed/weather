<script setup>
defineProps({
  msg: {
    type: String,
    required: true
  }
})
</script>

<template>
    <section class="vh-100">
      <div class="container py-5 h-100">

        <div class="row d-flex justify-content-center align-items-center h-100">
          <div class="col">

            <h3 class="mb-4 pb-2 fw-normal">Check the weather forecast</h3>

            <div class="input-group rounded mb-3">
              <input type="search" class="form-control rounded" v-model="address" placeholder="City" aria-label="Search"
                aria-describedby="search-addon" />
              <button @click="getWeather(address)" type="button">
                <span class="input-group-text border-0 fw-bold" id="search-addon">
                  Check!
                </span>
              </button>
            </div>

            <div class="mb-4 pb-2">
              <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1"
                  value="option1" checked />
                <label class="form-check-label" for="inlineRadio1">Celsius</label>
              </div>

              <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio2"
                  value="option2" />
                <label class="form-check-label" for="inlineRadio2">Farenheit</label>
              </div>
            </div>

            <div class="card shadow-0 border" v-if="weatherData">
              <div class="card-body p-4">

                <h4 class="mb-1 sfw-normal">{{ weatherData.name }}, {{ weatherData.sys ? weatherData.sys.country : '' }}</h4>
                <p class="mb-2">Current temperature: <strong>{{ temp }}°C</strong></p>
                <p>Feels like: <strong>{{ temp }}°C</strong></p>
                <p>Max: <strong>{{ temp_max }}°C</strong>, Min: <strong>{{ temp_min }}°C</strong></p>

                <div class="d-flex flex-row align-items-center">
                  <p class="mb-0 me-4 text-capitalize">{{ weatherData.weather ? weatherData.weather[0].description : '' }}</p>
                  <!-- <i class="fas fa-cloud fa-3x" style="color: #eee;"></i> -->
                  <font-awesome-icon icon="cloud" style="color: #eee; font-size: 3em;"/>
                </div>

              </div>
            </div>

          </div>
          <div class="col" >

            <div class="card bg-dark text-white" v-if="weatherData" style="border-radius: 40px;">
              <div class="bg-image" style="border-radius: 35px;" v-if="weatherData.weather">
                <img v-if="weatherData.weather[0].main == 'Snow'" 
                  src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-weather/draw1.webp"
                  style="filter: contrast(0.5); height: 30em;"
                  class="card-img" alt="snowy weather" />

                <img v-else-if="weatherData.weather[0].description == 'clear sky'" 
                  src="/images/clear_sky.jpg"
                  style="filter: contrast(0.8); height: 30em;"
                  class="card-img" alt="snowy weather" />

                <img v-else-if="weatherData.weather[0].main == 'Rain'" 
                  src="/images/rain.jpg"
                  style="filter: contrast(0.5); height: 30em;"
                  class="card-img" alt="snowy weather" />

                <img v-else-if="weatherData.weather[0].main == 'Clouds'" 
                  src="/images/cloud.jpg"
                  style="filter: contrast(0.8); height: 30em;"
                  class="card-img" alt="snowy weather" />
                
                <div class="mask" style="background-color: rgba(190, 216, 232, .5);"></div>
              </div>
              <div class="card-img-overlay text-dark p-5">
                <h4 class="mb-0">{{ weatherData.name }} - {{ weatherData.sys ? weatherData.sys.country : '' }}</h4>
                <p class="display-2 my-3">{{ temp }}°C</p>
                <p class="mb-2">Feels Like: <strong>{{ temp }}°C</strong></p>
                <h5>{{ weatherData.weather ? weatherData.weather[0].main : '' }}</h5>
              </div>
            </div>

            </div>
        </div>

      </div>
    </section>

    

</template>



<script>
import axios from 'axios';

const apiKey = '4a939bd0f061fb89daaa193bc38eff20';
const apiUrl = `http://api.openweathermap.org/data/2.5/weather?appid=${apiKey}`;

export default {
  watch: {
  },
    name : 'HelloWorld',
    components : {
      
    },
    data () {
      return {
        // load data
        loaded : true,  
        city: null,
        weatherData: null,
        temp: null
  
        
      }
    },
    computed : {
       
    },
    created () {
        // axios.get('/api/factures')
        // .then((res) => {
        //   this.factures = res.data.facture;
        //   this.loaded = false;
        // })
        // .catch((error) => {
        //     console.log(error);
        // })
    },
    methods: {
      // "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
      //         lat +
      //         "," +
      //         long +
      //         "&key=AIzaSyCphuhFS_F2AuS20BH0_WyGIP3vXWg53nA"
     

      getWeather: async function(city) {
          try {
            const response = await axios.get(`${apiUrl}&q=${city}`);
            this.weatherData = response.data
            this.temp = Math.round(parseFloat(this.weatherData.main.temp)-273.15);
            this.temp_min = Math.round(parseFloat(this.weatherData.main.temp_min)-273.15);
            this.temp_max = Math.round(parseFloat(this.weatherData.main.temp_max)-273.15);
          } catch (error) {
            console.error(error);
          }
      },


   
                  
      
    },
    
    mounted() {
    }

  }

  </script>
<style scoped>

</style>
