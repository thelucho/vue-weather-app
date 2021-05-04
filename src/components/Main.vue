<template>
  <div class="main" :class="weatherTemp">
    <div class="overlay"></div>
    <div class="searchBox">
      <input
        type="text"
        class="searchBar"
        placeholder="Ingrese una localidad"
        v-model="placement"
        @keypress="fetchWeather"
      >
      <i class="ri-search-line"></i>
    </div>

    <div class="wrap" v-if="dataExists">
      <div class="degrees">{{ roundedDegrees }} °C</div>
      <div class="date">{{ dateBuilder() }}</div>
      <div class="weather">
        <i :class="iconBuilder"></i>
        {{ weatherData.weather[0].main }}
      </div>
      <div class="location">{{ weatherData.name }}, {{ weatherData.sys.country }}</div>
    </div>

    <div class="error" v-if="errorMsg">
      <i class="ri-error-warning-line"></i>
      <span>Upsss!</span>
      No se encontró la localidad que buscabas
    </div>

  </div>
</template>

<script>
export default {
  name: 'Main',

  data() {
    return {
      placement: null,
      weatherData: {},
      errorMsg: false
    }
  },

  computed: {
    dataExists() {
      return this.weatherData.main != undefined
    },

    roundedDegrees() {
      return Math.round(this.weatherData.main.temp)
    },

    iconBuilder() {
      let icon = ''

      if (this.weatherData.weather[0].main === 'Clear') {
        return icon = 'ri-sun-line'
      }

      if (this.weatherData.weather[0].main === 'Clouds') {
        return icon = 'ri-cloudy-line'
      }

      if (this.weatherData.weather[0].main === 'Rain') {
        return icon = 'ri-rainy-line'
      }

      if (this.weatherData.weather[0].main === 'Haze') {
        return icon = 'ri-haze-line'
      }

      if (this.weatherData.weather[0].main === 'Drizzle') {
        return icon = 'ri-drizzle-line'
      }

      return icon
    },

    weatherTemp() {
      let temp = ''
      if (this.weatherData.main != undefined) {
        temp = this.weatherData.main.temp > 16 ? 'hot' : 'cold'
      }
      return temp
    }
  },

  methods: {
    fetchWeather(e) {
      this.errorMsg = false
      this.weatherData = {}
      if (e.key == 'Enter') {
        fetch(`${process.env.VUE_APP_BASE_URL}weather?q=${this.placement}&units=metric&appid=${process.env.VUE_APP_API_KEY}`)
          .then(res => {
            return res.json()
          })
          .then(res => {
            if (res.cod === 200) {
              this.setResults(res)
              this.errorMsg = false
            } else {
              this.errorMsg = true
            }
          })
        .catch((error) => {
          console.log(error)
          this.errorMsg = true
        })
      }
    },

    setResults(results) {
      console.log(results)
      this.weatherData = results
    },

    dateBuilder() {
      let d = new Date()
      let months = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio", "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"]
      let days = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes", "Sábado", "Domingo"]

      let day = days[d.getDay() - 1]
      let date = d.getDate()
      let month = months[d.getMonth()]
      let year = d.getFullYear()

      return `${day} ${date} ${month}, ${year}`
    }
  }
}
</script>

<style scoped lang="scss">
.main {
  height: 100vh;
  font-family: 'Open Sans', sans-serif;
  font-weight: 300;
  padding: 30px;
  box-sizing: border-box;
  background-image: linear-gradient(180deg, #6599cb, #66b5ce);
  background: url('../assets/normal.webp') center center no-repeat;
  background-size: cover;
  position: relative;
  transition: all .3s ease-in-out;

  &.hot {
    background: url('../assets/sunny.png') center center no-repeat;
    background-size: cover;
    transition: all .3s ease-in-out;
  }

  &.cold {
    background: url('../assets/cloudy.png') center center no-repeat;
    background-size: cover;
    transition: all .3s ease-in-out;
  }

  .overlay {
    position: absolute;
    z-index: 1;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgb(0 0 0 / 18%);
  }

  .searchBox {
    max-width: 400px;
    margin: 0 auto 28vh auto;
    position: relative;
    z-index: 2;

    .searchBar {
      font-family: 'Open Sans', sans-serif;
      font-weight: 300;
      width: 100%;
      box-sizing: border-box;
      height: 44px;
      border-radius: 30px;
      border: 1px solid #ccc;
      padding: 15px 47px 15px 23px;
      font-size: 16px;

      &:focus, &:active {
        outline: none;
      }
    }

    i {
      position: absolute;
      right: 20px;
      top: 12px;
      font-size: 20px;
      color: #b9b9b9;
    }
  }

  .wrap {
      display: flex;
      flex-flow: column;
      align-items: center;
      color: white;
      z-index: 3;
      position: relative;

      .location {
        font-size: 11px;
        font-weight: 300;
        letter-spacing: 2px;
        text-transform: uppercase;
        color: white;
      }

      .weather {
        font-weight: 600;
        font-style: initial;
        margin: 33px 0px;
        display: flex;
        flex-flow: column;
        align-items: center;

        i {
          font-size: 46px;
        }
      }

      .degrees {
        font-size: 62px;
        font-weight: 600;
        letter-spacing: -4.5px;
      }
  }

  .error {
    display: flex;
    flex-flow: column;
    align-items: center;
    padding-top: 50px;
    text-align: center;
    color: white;
    font-size: 19px;
    max-width: 250px;
    margin: 0 auto;
    z-index: 3;
    position: relative;

    i {
      font-size: 53px;
      color: #ff9797;
      margin-bottom: 15px;
    }

    span {
      font-size: 24px;
      font-weight: 500;
      margin-bottom: 15px;
    }
  }
}
</style>
