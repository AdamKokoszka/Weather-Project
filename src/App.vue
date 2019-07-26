<template>
  <div class="container">
    <div class="row">
      <div class="col-6 offset-3">
        <h1 class="text-center">Weather Application</h1>
        <select name="WeatherCity" class="form-control" @change="ChangeCity($event)" v-model="selected">
          <option v-for="city in cities" :value="city.id">{{city.name}}</option>
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
            <p class="Temperature Label Fl">{{cityInfo[0].temperature}} </p><sup>°F</sup>
          </div>
        </div>
        <div class="col-6">
          <div class="MainInfo">
            <p><span class="Label">Precipitation:</span> {{cityInfo[0].precipitation}}%</p>
            <p><span class="Label">Humidity:</span> {{cityInfo[0].humidity}}%</p>
            <p><span class="Label">Wind:</span> {{cityInfo[0].windInfo.speed}} mph {{cityInfo[0].windInfo.direction}}</p>
            <p><span class="Label">Pollen Count:</span> {{cityInfo[0].pollenCount}}</p>
          </div>
        </div>
        <div class="col-12">
          <div class="row">
            <app-weather-item v-for="(dayInfo, index) in cityInfo" :dayInfo="dayInfo" :index="index"></app-weather-item>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  import WeatherItem from './components/weatheritem.vue'
  export default {
    data() {
      return {
        cities: {},
        cityInfo: {},
        selected: '1',
        currentFullData: '',
        kokos: '',
        currentCity: 'Katowice',
        currentDate: new Date().toISOString().slice(0, 10),
        weatherName:[
          {name: 'Cloudy', text: 'Cloudy'},
           {name: 'PartialCloudy', text: 'Partial cloudy'},
           {name: 'RainAndCloudy', text: 'Rain and cloudy'},
           {name: 'RainLight', text: 'Light rain'},
           {name: 'Sunny', text: 'Sunny'}
        ],
        date: {
          dayOfWeek: ['Monday', 'Tuseday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
          months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
        }
      };
    },
    created() {
      axios.get('http://dev-weather-api.azurewebsites.net/api/city')
        .then(res => {
          this.cities = res.data;
        })
        .catch(error => console.log(error))
      axios.get('http://dev-weather-api.azurewebsites.net/api/city/' + this.selected + '/weather?date=' + this.currentDate)
        .then(res => {
          this.cityInfo = res.data;
        })
        .catch(error => console.log(error))
    },
    methods: {
      ChangeCity(event) {
        this.currentCityId = event.target.value;
        this.currentCityObj = this.cities.find(obj => obj.id == this.currentCityId);
        this.currentCity = this.currentCityObj.name;
        axios.get('http://dev-weather-api.azurewebsites.net/api/city/' + this.selected + '/weather?date=' + this.currentDate)
          .then(res => {
            this.cityInfo = res.data;
          })
          .catch(error => console.log(error))
      }
    },
    computed: {
      ImgPath() {
        return require('./assets/' + this.cityInfo[0].type + '.png')
      },
      currentDateFull(){
        let currentNumDay = new Date(this.currentDate).getDay();
        let currentdayOfWeek = this.date.dayOfWeek[ new Date(this.currentDate).getDay()];
        let currentMonth = this.date.months[ new Date(this.currentDate).getMonth()];
        this.currentFullData = currentdayOfWeek +', '+ currentMonth + ' ' + currentNumDay;
        let currentNumDayLength = currentNumDay.length -1;
        if(currentNumDay[currentNumDayLength] == 1){
          this.currentFullData += 'st'
        } else if(currentNumDay[currentNumDayLength] == 2){
          this.currentFullData += 'nd'
        } else if(currentNumDay[currentNumDayLength] == 3){
          this.currentFullData += 'rd'
        } else {
          this.currentFullData += 'th'
        }
        return this.currentFullData
      },
      typeWeather(){
        return this.weatherName.find(obj => obj.name === this.cityInfo[0].type).text;
      }
    },
    components: {
      appWeatherItem: WeatherItem
    }
  }

</script>

<style lang="scss">
  .Fl{
    float: left
  }
  .WeatherBox {
    width: 900px;
    margin: auto;
    margin-top: 20px;
    padding: 20px;
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
    .MainImg {
      width: 100px;
      float: left;
      margin-right: 10px;
    }

    p {
      line-height: 0.5;
      font-size: 17px;
    }
    sup{
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
  }

  .MainInfo {
    padding-top: 60px;
    padding-bottom: 20px;
  }

</style>
