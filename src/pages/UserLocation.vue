<template>
  <section class="ui two column centered grid">
    <div class="column">
      <form class="ui segment large form" :class="{ loading: spinner }">
        <div class="ui message red" v-show="error">{{ error }}</div>
        <div class="ui segement">
          <div class="field">
            <div class="ui right icon input large">
              <input
                type="text"
                placeholder="Enter Your Address"
                v-model="address"
                id="autocomplete"
              />
              <i class="dot circle icon link" @click="locatorButtonPressed"></i>
            </div>
          </div>
          <button class="ui button">Go</button>
        </div>
      </form>
    </div>
  </section>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      address: "",
      error: "",
      spinner: false
    };
  },
  mounted() {
    new google.maps.places.Autocomplete(
      document.getElementById("autocomplete"),
      {
        bounds: new google.maps.LatLngBounds(
          new google.maps.LatLng(45.508888, -73.561668)
        )
      }
    );
  },
  methods: {
    locatorButtonPressed() {
      this.spinner = true;
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          position => {
            this.getAddressFrom(
              position.coords.latitude,
              position.coords.longitude
            );
          },
          error => {
            this.spinner = false;
            this.error = "Your browser does not support geolocation API";
          }
        );
      } else {
        this.spinner = false;
        console.log("Your browser does not support geolocation API");
      }
    },
    getAddressFrom(lat, long) {
      console.log(process.env.GOOGLE_API_KEY);
      axios
        .get(
          `https://maps.googleapis.com/maps/api/geocode/json?latlng=${lat},${long}&key=${VUE_APP_GOOGLE_API_KEY}`
        )
        .then(response => {
          if (response.data.error_message) {
            console.log(response.data.error_message);
          } else {
            this.address = response.data.results[0].formatted_address;
            console.log(response.data.results[0].formatted_address);
          }
          this.spinner = false;
        })
        .catch(error => {
          this.spinner = false;
          this.error = error.message;
          console.log(error.message);
        });
    }
  }
};
</script>
<style>
.ui.button,
.dot.circle.icon {
  background-color: #ff5a5f;
  color: white;
}
.pac-item {
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}
.pac-item:hover {
  background-color: aquamarine;
}
.pac-item-query {
  font-size: 16px;
}
.pac-icon {
  display: none;
}
</style>
