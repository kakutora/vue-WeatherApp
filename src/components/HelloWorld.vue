<template>
  <div class="hello">
    <div class="currentTime">{{ now.getMonth() }}月{{ now.getDate() }}日{{ now.getHours() }}時{{ now.getMinutes() }}分</div>

    <div class="weather_main"><img v-if="Json_OW_CurrentLocation.data"
        :src="require(`@/assets/img/${this.item.imgsrc}.png`)"></div>

    <!-- <div v-if="Json_OW_CurrentLocation.data">現在の天気は{{ Json_OW_CurrentLocation.data.weather[0].main }}です。</div> -->

    <div class="temp" v-if="Json_OW_CurrentLocation.data">{{ Json_OW_CurrentLocation.data.main.temp }}°C</div>
    <div class="flexBox">
      <div class="temp_max" v-if="Json_OW_CurrentLocation.data">Max:{{ Json_OW_CurrentLocation.data.main.temp_max }}°C
      </div>
      <div class="temp_min" v-if="Json_OW_CurrentLocation.data">Min:{{ Json_OW_CurrentLocation.data.main.temp_min }}°C
      </div>
    </div>
    <div class="humidity" v-if="Json_OW_CurrentLocation.data">湿度{{ Json_OW_CurrentLocation.data.main.humidity }}%</div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'HellOW_Current_Location_Urlorld',
    data() {
      return {
        Json_OW_CurrentLocation: [],
        USER_Latitude: '',
        USER_Longitude: '',
        URL_CurrentLocation: '',
        now: new Date(),
        item: {
          imgsrc: 'question'
        }
      }
    },
    created() {
      this.get_OW_CurrentLocation_Json();
    },
    methods: {
      get_Current_Location() {
        return new Promise((res) => {
          navigator.geolocation.getCurrentPosition(function (pos) {
            let coords = pos.coords
            this.USER_Latitude = coords.latitude
            this.USER_Longitude = coords.longitude
            res()
          }.bind(this));
        })
      },
      OW_CurrentLocation_Url() {
        this.URL_CurrentLocation =
          `https://api.openweathermap.org/data/2.5/weather?lat=${this.USER_Latitude}&lon=${this.USER_Longitude}&units=metric&APPID=baaa5d6fda2e306b70b718bb7c7f7e93`
      },
      async get_OW_CurrentLocation_Json() {

        await this.get_Current_Location()
        this.OW_CurrentLocation_Url()
        axios
          .get(this.URL_CurrentLocation)
          .then(response => JSON.parse(JSON.stringify(response)))
          .then(parsed => this.Json_OW_CurrentLocation = parsed)
          //.then(cos => console.log(cos))
          .then(switchJson => this.switchImg(switchJson))
      },
      switchImg(val) {
        switch (val.data.weather[0].icon) {
          case '01d':
            this.item.imgsrc = 'clear_sky';
            break;
          case '02d':
            this.item.imgsrc = 'few_clouds';
            break;
          case '03d':
            this.item.imgsrc = 'scattered_clouds';
            break;
          case '04d':
            this.item.imgsrc = 'broken_clouds';
            break;
          case '09d':
            this.item.imgsrc = 'shower_rain';
            break;
          case '10d':
            this.item.imgsrc = 'rain';
            break;
          case '11d':
            this.item.imgsrc = 'thunderstorm';
            break;
          case '13d':
            this.item.imgsrc = 'snow';
            break;
          case '50d':
            this.item.imgsrc = 'mist';
            break;
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  div {
    text-align: center;
    font-size: 25px;
  }

  .currentTime {
    margin-top: 50px;
    text-align: center;
  }

  .weather_main {
    width: 50%;
    margin: 50px auto 50px;

    img {
      width: 100%;
      margin: 0 auto;
    }
  }

  .temp {
    margin-bottom: 50px;
  }

  .flexBox {
    display: flex;
    justify-content: center;
    gap: 50px;
    margin-bottom: 50px;
  }
</style>