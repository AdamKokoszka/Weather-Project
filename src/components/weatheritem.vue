<template>
  <div class="ItemSize">
    <div class="WeatherItem">
      <h6>{{dayOfWeek}}</h6>
      <img class="IconWeather" :src="ImgPath">
      <div class="row">
        <div class="col-12">{{dayInfo.temperature}}° <span class="Label">36°</span></div>
      </div>
      <div class="col-12">
        <div class="Label"><small>Pollen {{dayInfo.pollenCount}}</small></div>
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
    methods: {

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
    },
    created() {}
  }

</script>
<style lang="scss">
  .ItemSize {
    width: calc(100%/7);
  }

  .WeatherItem {
    text-align: center;
  }

  .IconWeather {
    width: 50%;
  }

</style>
