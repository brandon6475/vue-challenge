<template>
  <div class="cascading-dropdown">
    <div class="dropdown">
      <h2>Build a reusable dropdown vue component</h2>
      <div class="center">
        <div class="select-title">
          <span>Country:</span>
        </div>
        <div class="select-box">
          <select v-model="selectedCountry" @change="onChangeCountry">
            <option value="">Select a Country</option>
            <option
              v-for="(country, index) in listCountry"
              :value="country.country_name"
              :key="index"
            >
              {{ country.country_name }}
            </option>
          </select>
        </div>
      </div>
      <div v-if="listCountry.length == 0" class="lds-spinner"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      <p v-if="selectedCountry">Selected Country - {{ this.selectedCountry }}</p>
    </div>
    <div class="dropdown">
      <div class="center">
        <div class="select-title">
          <span>State:</span>
        </div>
        <div class="select-box">
          <select
            :disabled="listState.length == 0"
            v-model="selectedState"
            @change="onChangeState"
          >
            <option value="">Select a State</option>
            <option
              v-for="(state, index) in listState"
              :key="index"
              :value="state.state_name"
            >
              {{ state.state_name }}
            </option>
          </select>
        </div>
      </div>
      <div v-if="selectedCountry && listState.length == 0" class="lds-spinner"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      <p v-if="selectedState">Selected State - {{ this.selectedState }}</p>
    </div>
    <div class="dropdown">
      <div class="center">
        <div class="select-title">
          <span>City:</span>
        </div>
        <div class="select-box">
          <select multiple="true" :disabled="listCities.length == 0" v-model="selectedCity">
            <option v-if="!selectVariable" @click="selectAll">Select All</option>
            <option v-if="selectVariable" @click="unselectAll">Unselect All</option>
            <option
              v-for="(city, index) in listCities"
              :key="index"
              :value="city.city_name"
              :class="selectVariable?'selected-option':'unselected-option'"
              @click="commonFunc"
            >
              {{ city.city_name }}
            </option>
          </select>
        </div>
      </div>
      <div v-if="selectedCountry && selectedState && listCities.length == 0" class="lds-spinner"><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
      <p v-if="selectedCity">Selected City - {{ this.selectedCity }}<br/><br/>
      Number of selected cities is - {{this.selectedCity.length}}</p>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "Dropdown",
  data() {
    return {
      listCountry: [],
      listState: [],
      listCities: [],
      selectedCountry: "",
      selectedState: "",
      selectedCity: "",
      authToken: "",
      selectVariable: false
    };
  },
  created() {
    this.generateAccessToken();
  },
  methods: {
    generateAccessToken() {
      axios
        .get("https://www.universal-tutorial.com/api/getaccesstoken", {
          headers: {
            Accept: "application/json",
            "api-token":
              "fg0h_Zera8BKRwtaZlN9790B2KQdPOeyh2JlbMCIw0OYOqupB4x2JxRDkJBMAMnceEk",
            "user-email": "prashant.otpl@gmail.com",
          },
        })
        .then((res) => {
          this.authToken = res.data.auth_token;
          this.loadCountry();
        });
    },
    selectAll() {
      this.selectVariable = true;
      this.selectedCity = [];
      for(var i=0; i<this.listCities.length; i++){
        this.selectedCity.push(this.listCities[i].city_name);
      }
    },
    unselectAll() {
      this.selectVariable = false;
      this.selectedCity = [];
    },
    commonFunc() {
      this.selectVariable = false;
    },
    loadCountry() {
      axios
        .get("https://www.universal-tutorial.com/api/countries", {
          headers: {
            Authorization: `Bearer ${this.authToken}`,
            Accept: "application/json",
          },
        })
        .then((res) => {
          this.listCountry = res.data;
        });
    },
    onChangeCountry() {
      axios
        .get(
          `https://www.universal-tutorial.com/api/states/${this.selectedCountry}`,
          {
            headers: {
              Authorization: `Bearer ${this.authToken}`,
              Accept: "application/json",
            },
          }
        )
        .then((res) => {
          this.listState = res.data;
        });
        this.listCities = [];
        this.selectedState = "";
        this.selectedCity = "";
    },
    onChangeState() {
      axios
        .get(
          `https://www.universal-tutorial.com/api/cities/${this.selectedState}`,
          {
            headers: {
              Authorization: `Bearer ${this.authToken}`,
              Accept: "application/json",
            },
          }
        )
        .then((res) => {
          this.listCities = res.data;
        });
        this.selectedCity = "";
    },
  },
};
</script>
<style scoped>
  div:after{
    background: black !important;
  }
  select {
    width: 250px;
    padding:5px;
  }
  span {
    padding:5px;
  }
  .selected-option {
    background: "#1e90ff";
    color: "white";
  }
  .unselected-option {
    background: "white";
    color: "black";
  }
  .select-title {
    width: 80px;
    text-align: left;
  }
  .select-box {
    text-align: left;
  }
  .center {
    text-align: center;
    display: flex;
    justify-content: center;
  }
  .lds-spinner {
    color: official;
    display: inline-block;
    position: relative;
    width: 80px;
    height: 80px;
  }
  .lds-spinner div {
    transform-origin: 40px 40px;
    animation: lds-spinner 1.2s linear infinite;
  }
  .lds-spinner div:after {
    content: " ";
    display: block;
    position: absolute;
    top: 3px;
    left: 37px;
    width: 6px;
    height: 18px;
    border-radius: 20%;
    background: #fff;
  }
  .lds-spinner div:nth-child(1) {
    transform: rotate(0deg);
    animation-delay: -1.1s;
  }
  .lds-spinner div:nth-child(2) {
    transform: rotate(30deg);
    animation-delay: -1s;
  }
  .lds-spinner div:nth-child(3) {
    transform: rotate(60deg);
    animation-delay: -0.9s;
  }
  .lds-spinner div:nth-child(4) {
    transform: rotate(90deg);
    animation-delay: -0.8s;
  }
  .lds-spinner div:nth-child(5) {
    transform: rotate(120deg);
    animation-delay: -0.7s;
  }
  .lds-spinner div:nth-child(6) {
    transform: rotate(150deg);
    animation-delay: -0.6s;
  }
  .lds-spinner div:nth-child(7) {
    transform: rotate(180deg);
    animation-delay: -0.5s;
  }
  .lds-spinner div:nth-child(8) {
    transform: rotate(210deg);
    animation-delay: -0.4s;
  }
  .lds-spinner div:nth-child(9) {
    transform: rotate(240deg);
    animation-delay: -0.3s;
  }
  .lds-spinner div:nth-child(10) {
    transform: rotate(270deg);
    animation-delay: -0.2s;
  }
  .lds-spinner div:nth-child(11) {
    transform: rotate(300deg);
    animation-delay: -0.1s;
  }
  .lds-spinner div:nth-child(12) {
    transform: rotate(330deg);
    animation-delay: 0s;
  }
  @keyframes lds-spinner {
    0% {
      opacity: 1;
    }
    100% {
      opacity: 0;
    }
  }
</style>
