<template>
  <section class="vh-100" style="background-image: url('images/bg.jpg'); background-size: cover">
  <div class="container py-5 h-100">
    <div class="row d-flex justify-content-center align-items-center h-100">
      <div class="col-lg-6 col-md-8">
        <h3 class="text-center mb-4 fw-bold text-white">
          <i class="bi bi-cloud-sun-fill"></i> Weather Forecast
        </h3>

        
        <div class="position-relative">
          <div class="input-group input-group-lg shadow-sm">
            <input
              type="text"
              class="form-control border-end-0"
              v-model="city"
              @input="filterCities"
              placeholder="Enter city name"
              aria-label="City"
            />
            <button 
              class="btn btn-primary px-4"
              @click="getWeather(city)"
            >
              <span class="d-none d-sm-inline">Check Weather</span>
              <span class="d-sm-none">→</span>
            </button>
          </div>

          <!-- Dropdown Menu -->
          <div class="position-absolute w-100 mt-1" style="z-index: 1000;">
            <ul 
              v-if="filteredCities.length && city"
              class="list-group shadow-sm"
            >
              <li 
                v-for="(city, index) in filteredCities"
                :key="index"
                class="list-group-item list-group-item-action"
                style="cursor: pointer;"
                 @click="getWeather(city.name)"
              >
                <div class="d-flex justify-content-between align-items-center">
                  <div>
                    <span class="fw-medium">{{ city.name }}</span>
                    <span class="text-muted ms-2">{{ city.country }}</span>
                  </div>
                  <i class="bi bi-arrow-right text-primary opacity-0"></i>
                </div>
              </li>
            </ul>
          </div>
        </div>
        
        <div v-if="weather" class="card border-0 shadow-lg mt-3" style="border-radius: 20px;">
          <div class="card-body text-center">
            <h4 class="card-title fw-bold text-primary">
              {{ weather.name }}, {{ weather.sys?.country }}
            </h4>
            <p class="text-muted">
              {{ weather.weather[0]?.description }}
            </p>

            
            <div class="d-flex justify-content-center align-items-center">
              <img
                :src="getWeatherIcon(weather.weather[0]?.main)"
                alt="Weather icon"
                class="img-fluid me-3"
                style="max-width: 100px;"
              />
              <div>
                <h1 class="display-4 fw-bold text-primary">{{ temp }}°C</h1>
                <p class="mb-0">
                  Feels like: <strong>{{ temp }}°C</strong><br />
                  Max: <strong>{{ temp_max }}°C</strong>, Min: <strong>{{ temp_min }}°C</strong>
                </p>
              </div>
            </div>

            
            <div class="mt-4 ticker-container overflow-hidden position-relative">
              <div class="ticker-content d-flex" :style="{ animationDuration: `${getAnimationDuration()}s` }">
                <div
                  class="ticker-item px-4 py-2 text-white d-flex align-items-center"
                  v-for="(row, index) in getTodayWeather()"
                  :key="index"
                  style="background-color: rgb(213 224 253 / 36%); border-radius: 10px; margin-right: 10px;"
                >
                  <img
                    :src="getWeatherIcon(row.weather[0]?.main)"
                    alt="Weather icon"
                    class="me-3"
                    style="width: 40px;"
                  />
                  <span class="" style="color: #9eade9;">
                    {{ getTime(row.dt_txt) }} - {{ row.weather[0]?.description }}: 
                    {{ Math.round(parseFloat(row.main.temp) - 273.15) }}°C
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>

        
        <div v-if="unique && unique.length > 1" class="mt-4 text-white">
          <h5 class="fw-bold text-light">5-Day Forecast</h5>
          <div class="d-flex justify-content-between overflow-auto py-2">
            <div
              v-for="(row, i) in unique.slice(1, 6)"
              :key="i"
              class="text-center mx-2 bg-white shadow-sm rounded p-3"
              style="width: 150px;"
            >
              <p class="small fw-bold text-primary">
                {{ Math.round(row.main.temp - 273.15) }}°C
              </p>
              <img
                :src="getWeatherIcon(row.weather[0]?.main)"
                alt="Weather icon"
                class="img-fluid mb-2"
                style="width: 50px;"
              />
              <p class="small mb-0 text-primary">{{ getName(row.dt_txt) }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

</template>


<style scoped>
.vh-100 {
  min-height: 100vh;
}
.card {
  border-radius: 10px;
}
.input-group input {
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
}
.input-group button {
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}
.dropdown-menu {
  position: absolute;
  z-index: 1000;
  max-height: 200px;
  overflow-y: auto;
}
</style>




<script>
import axios from 'axios';

const apiKey = '4a939bd0f061fb89daaa193bc38eff20';
const apiUrl = `http://api.openweathermap.org/data/2.5/weather?appid=${apiKey}`;
const apiUrlFiveDays = `http://api.openweathermap.org/data/2.5/forecast?appid=${apiKey}`;
import cities from "@/assets/cities.json"; // Load local JSON file

export default {
  watch: {
  },
    name : 'HelloWorld',
    components : {
      
    },
    data () {
      return {
        loaded : true,  
        city: null,

        weather: null,
        weatherToday: null,
        weatherFiveDays: null,
        temp: null,
        unique: null,
        weatherFiveDays: {
          list: [], 
        },
        cities: [],
        filteredCities: [] 

        
      }
    },
    computed : {
       
    },
    created() {
      this.cities = cities;
    },
    methods: {
      filterCities() {
        if (!this.city) {
          this.filteredCities = [];
          return;
        }
        this.filteredCities = this.cities
          .filter(city => city.name.toLowerCase().startsWith(this.city.toLowerCase()))
          .slice(0, 10); 
      },
      selectCity(city) {
        this.city = city;
        this.filteredCities = [];
      },
      getTodayWeather() {
        const today = new Date().toISOString().split("T")[0];
        return this.weatherFiveDays.list.filter((row) =>
          row.dt_txt.startsWith(today)
        ).reverse();
      },
      getAnimationDuration() {
        const itemCount = this.getTodayWeather().length;
        const durationPerItem = 3;
        return itemCount * durationPerItem;
      },
      getWeatherIcon(condition) {
        const icons = {
          Snow: "/images/icons/snow.png",
          Rain: "/images/icons/rain.png",
          Clouds: "/images/icons/cloud.png",
          Clear: "/images/icons/clear.png",
          Mist: "/images/icons/mist.png",
        };
        return icons[condition] || "/images/icons/default.png";
      },
      getName(datetime) {
        return new Date(datetime).toLocaleDateString("en-US", {
          weekday: "short",
        });
      },
     

      getWeather: async function(city) {
          try {
            
            const response = await axios.get(`${apiUrl}&q=${city}`);
            this.weather = response.data
            this.temp = Math.round(parseFloat(this.weather.main.temp)-273.15);
            this.temp_min = Math.round(parseFloat(this.weather.main.temp_min)-273.15);
            this.temp_max = Math.round(parseFloat(this.weather.main.temp_max)-273.15);
            this.getWeatherforFiveDays(this.weather.name);
            this.city = this.weather.name;
            this.filteredCities = [];
          } catch (error) {
            console.error(error);
          }
      },

      getWeatherforFiveDays : async function(city) {
        try{
          const response = await axios.get(`${apiUrlFiveDays}&q=${city}`);
          this.weatherFiveDays = response.data;
          let today = new Date().toJSON().slice(0,10).replace(/-/g,'-');


          this.unique = this.weatherFiveDays.list.reduce((acc, curr) => {
          if (acc.length === 0) {
              acc.push(curr)
          } else {
                  const found = acc.find(e => e.dt_txt.slice(0, 10) === curr.dt_txt.slice(0, 10))
                  if (!found) {
                      acc.push(curr)
                  }
              }
              return acc
          }, [])

        } catch (error) {
          console.error(error);
        }
      } ,

      getDate(datetime) {
        let date = new Date(datetime).toJSON().slice(0,10).replace(/-/g,'/')
        return date
      },

      getName(datetime) {
        let date = new Date(datetime).toLocaleString('en-us', {weekday:'long'})
        return date
      },

      getTime(datetime) {
        let time = new Date(datetime).toLocaleTimeString(navigator.language, {hour: '2-digit', minute:'2-digit'});
        return time 
      },   
    
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
.weather-box {
  width: 120px;
  height: 150px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: rgba(255, 255, 255, 0.8);
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

.ticker-container {
  position: relative;
  overflow: hidden;
  white-space: nowrap;
  width: 100%;
  display: flex;
  align-items: center;
}

.ticker-content {
  display: flex;
  align-items: center;
  animation: ticker-animation linear infinite;
  will-change: transform;
}

.ticker-item {
  display: flex;
  align-items: center;
  margin-right: 20px;
}

.ticker-icon {
  width: 30px;
  height: 30px;
  margin-right: 10px;
}

.ticker-text {
  font-size: 14px;
  color: #333;
}
.search-container {
  margin-top: 2rem;
  padding: 0 1rem;
}

@media (max-width: 640px) {
  .search-container {
    margin-top: 1rem;
  }
}

@keyframes ticker-animation {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(100%);
  }
}

</style>
