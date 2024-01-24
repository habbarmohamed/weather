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

            <!-- <div class="mb-4 pb-2">
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
            </div> -->

            <div class="card shadow-0 border" v-if="weatherData">
              <div class="card-body p-4">

                <h4 class="mb-1 sfw-normal">{{ weatherData.name }}, {{ weatherData.sys ? weatherData.sys.country : '' }}</h4>
                <p class="mb-2">Current temperature: <strong>{{ temp }}°C</strong></p>
                <p>Feels like: <strong>{{ temp }}°C</strong></p>
                <p>Max: <strong>{{ temp_max }}°C</strong>, Min: <strong>{{ temp_min }}°C</strong></p>

                <div class="d-flex flex-row align-items-center">
                  <p class="mb-0 me-4 text-capitalize">{{ weatherData.weather ? weatherData.weather[0].description : '' }}</p>
                  <!-- <i class="fas fa-cloud fa-3x" style="color: #eee;"></i> -->
                  <!-- <font-awesome-icon icon="cloud" style="color: #eee; font-size: 3em;"/> -->
                  <img v-if="weatherData.weather[0].main == 'Snow'" 
                    src="/images/icons/snow.png"
                    style="width: 15%;"
                    class="card-img" alt="snowy weather" />

                  <img v-else-if="weatherData.weather[0].description == 'clear sky'" 
                    src="/images/icons/clear.png"
                    style="width: 15%;"
                    class="card-img" alt="snowy weather" />

                  <img v-else-if="weatherData.weather[0].main == 'Rain'" 
                    src="/images/icons/rain.png"
                    style="width: 15%;"
                    class="card-img" alt="snowy weather" />

                  <img v-else-if="weatherData.weather[0].description == 'overcast clouds'" 
                    src="/images/icons/overcast.png"
                    style="width: 15%;"
                    class="card-img" alt="snowy weather" />
                </div>



              </div>
            </div>

          </div>
          <div class="col" >

            <div class="card text-white" v-if="weatherData">
              <div class="bg-image" v-if="weatherData.weather">
                <img v-if="weatherData.weather[0].main == 'Snow'" 
                  src="https://mdbcdn.b-cdn.net/img/Photos/new-templates/bootstrap-weather/draw1.webp"
                  style="filter: contrast(0.5); height: 30em;"
                  class="card-img" alt="snowy weather" />

                <img v-else-if="weatherData.weather[0].description == 'clear sky'" 
                  src="/images/clear_sky.jpg"
                  style="filter: contrast(0.8); height: 30em;"
                  class="card-img" alt="clear weather" />

                <img v-else-if="weatherData.weather[0].main == 'Rain'" 
                  src="/images/rain.jpg"
                  style="filter: contrast(0.5); height: 30em;"
                  class="card-img" alt="rain weather" />

                <img v-else-if="weatherData.weather[0].main == 'Clouds'" 
                  src="/images/cloud.jpg"
                  style="filter: contrast(0.8); height: 30em;"
                  class="card-img" alt="clouds weather" />
                <img v-else-if="weatherData.weather[0].main == 'Mist'" 
                  src="/images/mist.jpg"
                  style="filter: contrast(0.8); height: 30em;"
                  class="card-img" alt="mist weather" />
                
                <div class="mask" style="background-color: rgba(190, 216, 232, .5);"></div>
              </div>
              <div class="card-img-overlay text-dark p-5">
                <h4 class="mb-0">{{ weatherData.name }} - {{ weatherData.sys ? weatherData.sys.country : '' }}</h4>
                <p class="display-2 my-3">{{ temp }}°C</p>
                <p class="mb-2">Feels Like: <strong>{{ temp }}°C</strong></p>
                <h5>{{ weatherData.weather ? weatherData.weather[0].main : '' }}</h5>



                <!-- forecast 5 days  -->
              <div class="card" style="border-radius: 5px;background-color: #ffffff54;border: 0;">
                <div class="card-body p-2">

                  <div id="demo3" class="carousel slide" data-ride="carousel">
                      <!-- Carousel inner -->
                      <div class="carousel-inner">
                        <div class="carousel-item active">
                          <div class="d-flex justify-content-around text-center mb-4 pb-3 pt-2">
                            <div class="flex-column" v-for="(row, i) in weatherDataFiveDays.list.slice(0,6)" :key="i">
                              <p class="small"><strong>{{ Math.round(parseFloat(row.main.temp)-273.15) }}°C</strong></p>
                              <!-- <img 
                                src="/images/icons/overcast.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" /> -->
                              <img v-if="weatherData.weather[0].main == 'Snow'" 
                                src="/images/icons/snow.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" />

                              <img v-else-if="weatherData.weather[0].description == 'clear sky'" 
                                src="/images/icons/clear.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" />

                              <img v-else-if="weatherData.weather[0].main == 'Rain'" 
                                src="/images/icons/rain.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" />

                              <img v-else-if="weatherData.weather[0].description == 'overcast clouds'" 
                                src="/images/icons/overcast.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" />
                              <img v-else-if="weatherData.weather[0].description == 'few clouds'" 
                                src="/images/icons/cloud.png"
                                style="width: 75%;"
                                class="card-img" alt="snowy weather" />
                              <p class="mb-0"><strong>Mon</strong></p>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>

                  </div>
              </div>



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
const apiUrlFiveDays = `http://api.openweathermap.org/data/2.5/forecast?appid=${apiKey}`;

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
        weatherDataToday: null,
        weatherDataFiveDays: null,

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
            this.getWeatherforFiveDays(this.weatherData.name);
          } catch (error) {
            console.error(error);
          }
      },

      getWeatherforFiveDays : async function(city) {
        try{
          const response = await axios.get(`${apiUrlFiveDays}&q=${city}`);
          this.weatherDataFiveDays = response.data;
          let today = new Date().toJSON().slice(0,10).replace(/-/g,'-');
          // console.log(today);
          // this.weatherDataToday = this.weatherDataFiveDays.list.filter(e => () {
          //   var date_today = e.dt_txt
          // });

        } catch (error) {
          console.error(error);
        }
      } 


   
                  
      
    },
    
    mounted() {
      
    }

  }

  </script>
<style scoped>
.gradient-custom {
/* fallback for old browsers */
background: #ffffff;

/* Chrome 10-25, Safari 5.1-6 */
background: -webkit-linear-gradient(to right, rgba(255, 255, 255, 1), rgba(255, 236, 210, 1));

/* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
background: linear-gradient(to right, rgba(255, 255, 255, 1), rgba(255, 236, 210, 1))
}

.carousel-indicators li {
background-color: #282828;
width: 7px;
height: 7px;
border-radius: 50%;
}
</style>
