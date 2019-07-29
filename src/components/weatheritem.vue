<template>
  <div class="ItemSize Fl">
    <div class="WeatherItem">
      <div class="row">
        <div class="col-3 col-lg-12">
          <h6>{{dayOfWeek}}</h6>
        </div>
        <div class="col-3 col-lg-12">
          <img class="IconWeather" :src="ImgPath">
        </div>
        <div class="col-3 col-lg-12"><p>{{dayInfo.temperature}}° <span class="Label">36°</span></p></div>
        <div class="col-3 col-lg-12">
          <div class="Label"><small><p>Pollen {{dayInfo.pollenCount}}</p></small></div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    props: ['dayInfo', 'index'],
    data() {
      return {
        numDay: '',
        daysWeek: ['Monday', 'Tuseday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
      };
    },
    computed: {
      ImgPath() {
        return require('../assets/' + this.dayInfo.type + '.png')
      },
      dayOfWeek() {
        if (this.index != 0) {
          this.numDay = new Date(this.dayInfo.date).getDay();
          return this.daysWeek[this.numDay];
        }
        return 'Today';
      }
    }
  }

</script>
<style lang="scss">
  .Fl {
    float: left
  }
  .ItemSize {
    width: calc(100%/7);
  }
  .WeatherItem {
    text-align: center;
  }
  .IconWeather{
    width: 50%;
  }
  // Extra large
  @media (max-width: 1199.98px) {
    
  }

  // Large
  @media (max-width: 991.98px) {
    .ItemSize {
      width: 100%;
      border-top: 1px solid #dddddd;
      position: relative;
    }
    h6, .WeatherItem p{
      position: absolute;
      top: 50%;
      -ms-transform: translate(0%, -50%);
      transform: translate(0%, -50%);
      text-align: left;
    }
  }

  // Medium
  @media (max-width: 767.98px) {

  }
  
  //Small
  @media (max-width: 575.98px) { 
    .WeatherItem p{
      line-height: 1.2 !important;
      text-align: center;
    }
    .WeatherItem h6{
      margin: auto;
    }
    .IconWeather{
      width: 100%;
    }
  }


</style>
