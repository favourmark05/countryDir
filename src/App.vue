<template>
  <div id="app">
    <main>
      <header>
        <h1 class="text-white text-center py-5">
          <img src="../src/assets/wejapa1.png" />
          {{ title }}
        </h1>
      </header>
      <div class="content container bg-white">
        <div class="row py-5">
          <div class="col text-center">
            <h1 class="text-center">Please select</h1>
            <!-- for country -->
            <div class="countries-region py-5">
              <label for="countries" class="Country">Select Country</label> &nbsp; &nbsp; &nbsp;
              <select
                name="countries"
                id="countries"
                v-model="selectedCountry"
                @change="onCountrySelect($event)"
              >
                <option
                  :value="country.name"
                  v-for="country in countries"
                  :key="country.id"
                >{{ country.name }}</option>
              </select>
            </div>

            <!-- for state -->
            <div class="state-region py-5">
              <label for="state" class="state">select state</label> &nbsp; &nbsp; &nbsp;
              <select
                name="states"
                id="states"
                v-model="selectedState"
                @change="onStateSelect($event)">
                <option :value="state.name" v-for="state in states" :key="state.id">{{ state.name }}</option>
              </select>
              <div v-if="stateError" :class="{empty: stateError}">
                <p class="errorMessage">This Country Has No States!</p>
              </div>
            </div>

            <!-- for cities -->

            <div class="cities py-5">
              <label for="cities">Select City</label>&nbsp; &nbsp; &nbsp;
              <select
                name="cities"
                id="cities"
                v-model="selectedCity"
                class="count-select">
                <option
                  :value="city.name"
                  v-for="city in cities"
                  :key="city.id"
                  class="count-option">{{ city.name }}</option>
              </select>
              <div v-if="cityError" :class="{empty: cityError}">
                <p class="errorMessage">This State Has No Cities!</p>
              </div>
            </div>
          </div>
          <div class="col">
            <!-- this displays selected options -->
            <div class="text-center">
              <h1 class="text-center ">Result</h1>
              <div class="selected-options pt-5">
                <p v-if="selectedCountry">
                  <span>Country:</span>
                  {{ selectedCountry }}
                </p>
                <br/>

                <p v-if="selectedState">
                  <span>State:</span>
                  {{ selectedState }}
                </p>
                <br />

                <p v-if="selectedCity">
                  <span>City:</span>
                  {{ selectedCity }}
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      title: "Country Directory",
      selectedCountry: "",
      selectedState: "",
      selectedCity: "",
      stateError: false,
      cityError: false,
      countries: [],
      states: [],
      cities: []
    }
  },
  created() {
    this.$http
      .get(
        "https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/countries%2Bstates%2Bcities.json"
      )
      .then((ress) => {
        return ress.json();
      })
      .then((data) => {
        this.countries = data.slice(0, 251);
      });
  },
  methods: {
    onCountrySelect(event) {
      const state = event.target.value
      const index = this.countries.findIndex((x=>x.name === state))
      const countryState = this.countries[index]['states']
      if (countryState.length === 0) {
        this.stateError = !this.stateError
        this.states = ""
        this.cities = ""
      } else {
        this.states = countryState
        this.stateError = false
      }
  },
  onStateSelect(event) {
    const state = event.target.value;
    const index = this.states.findIndex((x) => x.name === state);
    const city = this.states[index]["cities"];
    if (city.length === 0) {
      this.cityError = !this.cityError;
    } else {
      this.cities = city;
      this.cityError = false;
    }
  },
}

}
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Fira+Sans:wght@500&display=swap");
#app {
  background-image: url("../src/assets/bg.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  height: 100vh;
}
header > h1 {
  font-family: "Fira Sans", sans-serif;
  font-size: 9vh;
}
header > h1 > img {
  height: 70px;
  width: 70px;
  border-radius: 50%;
  box-shadow: 3px 6px rgb(16, 119, 59);
}
.errorMessage{
  text-align: center;
  color: red;
  font-weight: bolder;
  font-size: 30px;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  /* background-color: red; */
}
.bg-white{
  /* background: transparent !important; */
  opacity: 0.8;
  border-radius: 5%;
}
</style>
