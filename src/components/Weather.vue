<template>
  <div class="container custom-container">
   <div class="row">
      <div class="col-12">
        <h1 class="text-center">Weather Application</h1>
      </div>
    </div>
    <div class="row">
      <div class="col-12 offset-0 col-lg-6 offset-lg-3">
        <select name="WeatherCity" class="form-control" @change="ChangeCity($event)" v-model="selected">
          <option v-for="city of cities" :key="city.id" :value="city.id">{{city.name}}</option>
        </select>
      </div>
    </div>
    <div class="WeatherBox">
      <div class="row">
        <div class="col-12">
          <h1>{{currentCity}}</h1>
        </div>
        <div class="col-6">
          <div class="CurrentInfo">
            <p>{{currentDateFull}}</p>
            <p>{{typeWeather}}</p>
            <img class="MainImg" :src="ImgPath">
            <p class="Temperature Label Fl">{{currentWeatherInfo.temperature}} </p><sup>°F</sup>
          </div>
        </div>
        <div class="col-6">
          <div class="MainInfo">
            <p><span class="Label">Precipitation:</span> {{currentWeatherInfo.precipitation}}%</p>
            <p><span class="Label">Humidity:</span> {{currentWeatherInfo.humidity}}%</p>
            <p><span class="Label">Wind:</span> {{currentWeatherInfo.windSpeed}} mph {{currentWeatherInfo.windDirection}}</p>
            <p><span class="Label">Pollen Count:</span> {{currentWeatherInfo.pollenCount}}</p>
          </div>
        </div>
        <div class="col-12">
          <app-weather-item v-for="(dayInfo, index) in cityInfo" :key="index" :dayInfo="dayInfo" :index="index"></app-weather-item>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  import WeatherItem from './WeatherItem.vue'
  export default {
    data() {
      return {
        cities: {},
        cityInfo: {},
        selected: '1',
        currentCity: 'Katowice',
        currentDate: new Date().toISOString().slice(0, 10),
        currentWeatherInfo: {
          temperature: '',
          precipitation: '',
          humidity: '',
          windSpeed: '',
          windDirection: '',
          pollenCount: '',
          type: ''
        },
        weatherName: [{
            name: 'Cloudy',
            text: 'Cloudy'
          },
          {
            name: 'PartialCloudy',
            text: 'Partial cloudy'
          },
          {
            name: 'RainAndCloudy',
            text: 'Rain and cloudy'
          },
          {
            name: 'RainLight',
            text: 'Light rain'
          },
          {
            name: 'Sunny',
            text: 'Sunny'
          }
        ],
        date: {
          dayOfWeek: ['Monday', 'Tuseday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
          months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
        }
      };
    },
    mounted() {
      axios.get('http://dev-weather-api.azurewebsites.net/api/city').then(res => {
          this.cities = res.data;
        })
        .catch(error => console.log(error))
      
      this.ChangeCity();
    },
    methods: {
      ChangeCity(event) {
        if(event){ 
          this.currentCityId = event.target.value;
          this.currentCityObj = this.cities.find(obj => obj.id == this.currentCityId);
          this.currentCity = this.currentCityObj.name;
        }
        
        axios.get('http://dev-weather-api.azurewebsites.net/api/city/' + this.selected + '/weather?date=' + this.currentDate).then(res => {
            this.cityInfo = res.data;
            this.currentWeatherInfo = res.data[0];
            this.currentWeatherInfo.precipitation = res.data[0].precipitation;
            this.currentWeatherInfo.humidity = res.data[0].humidity;
            this.currentWeatherInfo.windSpeed = res.data[0].windInfo.speed;
            this.currentWeatherInfo.windDirection = res.data[0].windInfo.direction;
            this.currentWeatherInfo.pollenCount = res.data[0].pollenCount;
            this.currentWeatherInfo.type = res.data[0].type;
          })
          .catch(error => console.log(error))
      }
    },
    computed: {
      ImgPath() {
        if (this.currentWeatherInfo.type) {
          return require('../assets/' + this.currentWeatherInfo.type + '.png')
        }
      },
      currentDateFull() {
        let currentNumDay = new Date(this.currentDate).getDate();
        let currentdayOfWeek = this.date.dayOfWeek[new Date(this.currentDate).getDay()];
        let currentMonth = this.date.months[new Date(this.currentDate).getMonth()];
        let currentDateFull = currentdayOfWeek + ', ' + currentMonth + ' ' + currentNumDay;
        let currentNumDayLength = currentNumDay.length - 1;
        if (currentNumDay[currentNumDayLength] == 1) {
          currentDateFull += 'st'
        } else if (currentNumDay[currentNumDayLength] == 2) {
          currentDateFull += 'nd'
        } else if (currentNumDay[currentNumDayLength] == 3) {
          currentDateFull += 'rd'
        } else {
          currentDateFull += 'th'
        }
        return currentDateFull
      },
      typeWeather() {
        if (this.currentWeatherInfo.type) {
          return this.weatherName.find(obj => obj.name === this.currentWeatherInfo.type).text;
        }
      }
    },
    components: {
      appWeatherItem: WeatherItem
    }
  }

</script>

<style lang="scss">
  .Fl {
    float: left
  }

  .WeatherBox {
    width: 80%;
    margin: auto;
    margin-top: 20px;
    padding: 0px 20px;
    border: 1px solid #e5e5e5;
    background-color: #fdfdfd;
    border-radius: 7px;
  }

  ul {
    list-style: none;
  }

  .Label {
    color: #676767;
  }

  h1 {
    font-size: 3rem;
    margin-bottom: 20px;
  }

  p {
    line-height: 0.7;
  }

  .CurrentInfo {
    p {
      line-height: 0.5;
    }
  }

  .MainImg {
    width: 100px;
    float: left;
    margin-right: 10px;
  }

  sup {
    font-size: 20px;
    margin-left: 5px;
    float: left;
    line-height: 73px;
    color: #676767;
  }

  .Temperature {
    font-size: 45px;
    padding-top: 25px;
    font-weight: 600;
  }

  .MainInfo {
    padding-top: 60px;
    padding-bottom: 20px;
  }

  //Large
  @media (max-width: 991.98px) {
    .WeatherBox {
      width: 100%;
    }
  }

  //Small
  @media (max-width: 575.98px) {
    html {
      font-size: 13px !important;
    }

    p {
      line-height: 0.7 !important;
    }

    .Temperature {
      padding-top: 15px;
      font-size: 30px;
    }

    sup {
      line-height: 47px;
      font-size: 15px;
      margin-left: 2px;
    }

    .CurrentInfo .MainImg {
      width: 55px;
      margin-top: 5px;
    }
  }

</style>
