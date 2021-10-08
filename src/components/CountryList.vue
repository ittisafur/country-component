<template>
  <div style="display: flex">
    <div class="sample" style="margin-right: 10px">
      <label for="custom-select-input">Select country</label>
      <div class="hidden-visually" aria-live="polite"></div>
      <div class="custom-select" id="myCustomSelect">
        <input type="text" @focus="openDropdown" class="select-css" />
        <span class="custom-select-icons" v-if="loading">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            style="
              margin: auto;
              background: rgb(255, 255, 255);
              display: block;
              shape-rendering: auto;
            "
            width="20px"
            height="20px"
            viewBox="0 0 100 100"
            preserveAspectRatio="xMidYMid"
          >
            <g>
              <path
                d="M50 15A35 35 0 1 0 74.74873734152916 25.251262658470843"
                fill="none"
                stroke="#e15b64"
                stroke-width="12"
              ></path>
              <path d="M49 3L49 27L61 15L49 3" fill="#e15b64"></path>
              <animateTransform
                attributeName="transform"
                type="rotate"
                repeatCount="indefinite"
                dur="1s"
                values="0 50 50;360 50 50"
                keyTimes="0;1"
              ></animateTransform>
            </g>
          </svg>
        </span>
        <ul class="custom-select-options" :class="{ 'hidden-all': !isOpen }">
          <li
            @click="
              selected_country = country;
              isOpen = false;
            "
            v-for="country in countries"
            :key="country.country_short_name"
          >
            <strong>{{ country.country_name }}</strong>
          </li>
        </ul>
      </div>
    </div>
    <div>
      <h1 v-if="selected_country">
        Selected country: {{ selected_country.country_name }}
      </h1>

      <p>States</p>
      <ul>
        <li
          @click="selected_state = state"
          v-for="state in selected_county_states"
          :key="state.state_name"
        >
          {{ state.state_name }}
        </li>
      </ul>
    </div>

    <div style="margin-left: 15px" v-if="selected_state">
      <h1>Selected State: {{ selected_state }}</h1>

      <p>Cities</p>
      <ul>
        <li v-for="city in selected_state_cities" :key="city.state_name">
          <input
            type="checkbox"
            :id="city.city_name"
            :value="city.city_name"
            v-model="checked_cities"
          />
          <label :for="city.city_name">{{ city.city_name }}</label>
        </li>
      </ul>

      Selected country count: {{ checked_cities.length }}
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  async mounted() {
    this.loading = true;
    const { data } = await axios.get(
      "https://www.universal-tutorial.com/api/countries/",
      {
        headers: {
          Authorization:
            "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7InVzZXJfZW1haWwiOiJpdHRpc2FmdXJAZ21haWwuY29tIiwiYXBpX3Rva2VuIjoiV3Y0RWFKemhXUEJRaFE1bnhHN01Sek82YTZsakxPTUZsV1ZkOGtJRmpDOE5RbTJHTngyZFhMWDJrVl9Bd2dGcG45USJ9LCJleHAiOjE2MzM4MDg5MjJ9.zoLrAh9fEbKu7hkBi7FYqch0WOz5RfyqqKomrYz_GNY",
        },
      }
    );

    this.loading = false;
    this.countries = data;
  },
  data() {
    return {
      loading: false,
      countries: [],
      isOpen: false,
      selected_country: {},
      selected_county_states: [],
      selected_state: {},
      selected_state_cities: [],
      checked_cities: [],
    };
  },
  watch: {
    async selected_country(country) {
      this.checked_cities = [];
      const { data: states } = await axios.get(
        `https://www.universal-tutorial.com/api/states/${country.country_name}`,
        {
          headers: {
            Authorization:
              "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7InVzZXJfZW1haWwiOiJpdHRpc2FmdXJAZ21haWwuY29tIiwiYXBpX3Rva2VuIjoiV3Y0RWFKemhXUEJRaFE1bnhHN01Sek82YTZsakxPTUZsV1ZkOGtJRmpDOE5RbTJHTngyZFhMWDJrVl9Bd2dGcG45USJ9LCJleHAiOjE2MzM4MDg5MjJ9.zoLrAh9fEbKu7hkBi7FYqch0WOz5RfyqqKomrYz_GNY",
          },
        }
      );

      this.selected_county_states = states;
    },
    async selected_state(state) {
      this.checked_cities = [];
      const { data: cities } = await axios.get(
        `https://www.universal-tutorial.com/api/cities/${state.state_name}`,
        {
          headers: {
            Authorization:
              "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7InVzZXJfZW1haWwiOiJpdHRpc2FmdXJAZ21haWwuY29tIiwiYXBpX3Rva2VuIjoiV3Y0RWFKemhXUEJRaFE1bnhHN01Sek82YTZsakxPTUZsV1ZkOGtJRmpDOE5RbTJHTngyZFhMWDJrVl9Bd2dGcG45USJ9LCJleHAiOjE2MzM4MDg5MjJ9.zoLrAh9fEbKu7hkBi7FYqch0WOz5RfyqqKomrYz_GNY",
          },
        }
      );
      this.selected_state_cities = cities;
    },
  },
  methods: {
    openDropdown() {
      if (this.countries.length) this.isOpen = true;
    },
  },
};
</script>

<style>
body {
  font-family: sans-serif;
  background: #eeeeee;
}
.wrapper {
  margin: 1em auto;
  text-align: left;
}
.sample {
  border: 1px solid #eeeeee;
  background: #ffffff;
  max-width: 30em;
  padding: 1em;
  margin-bottom: 1em;
}
.custom-select {
  position: relative;
}
.select-css {
  display: block;
  font-size: 1em;
  font-family: sans-serif;
  font-weight: 700;
  color: #444;
  line-height: 1.3;
  padding: 0.6em 1.4em 0.5em 0.8em;
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  margin: 0;
  border: 1px solid #aaa;
  box-shadow: 0 1px 0 1px rgba(0, 0, 0, 0.04);
  border-radius: 0.25em;
  -moz-appearance: none;
  -webkit-appearance: none;
  appearance: none;
  background-color: #fff;
  position: relative;
  z-index: 10;
}
.select-css::-ms-expand {
  display: none;
}
.select-css:hover {
  border-color: #888;
}
.select-css:focus {
  border: 2px dashed blue;
  color: #222;
  outline: none;
}
.custom-select-icons {
  position: absolute;
  top: 0.5em;
  right: 0.5em;
  z-index: 20;
  border: 1px solid white;
  background: transparent;
}
.custom-select-options {
  border: 1px solid #aaa;
  border-radius: 0 0 0.25em 0.25em;
  line-height: 1.5;
  margin: 0;
  margin-top: -0.5em;
  padding: 0;
  list-style-type: none;
  font-weight: normal;
  cursor: pointer;
  z-index: 2;
  position: absolute;
  width: calc(100% - 1px);
  background-color: #ffffff;

  max-height: 80vh;
  overflow-y: auto;
}
.custom-select-options li {
  padding: 1em;
}
.custom-select-options li:hover {
  background: blue;
  color: #fff;
  border: 1px solid blue;
  border-width: 0 0 0 1px;
}

.custom-select-options li:focus {
  border: 1px solid blue;
}
.icon {
  fill: ButtonText;
  pointer-events: none;
}
@media screen and (-ms-high-contrast: active) {
  .icon {
    fill: ButtonText;
  }
}
.hidden-all {
  display: none;
}
.hidden-visually {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  -webkit-clip-path: inset(50%);
  clip-path: inset(50%);
  border: 0;
}
</style>
