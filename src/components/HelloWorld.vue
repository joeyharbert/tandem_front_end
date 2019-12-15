<template>
  <v-container>
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        ></v-img>
      </v-flex>

      <v-flex mb-4>
        <h1 class="display-2 font-weight-bold mb-3">
          Welcome to PLANTZ
        </h1>
      </v-flex>


    </v-layout>

    <v-container fluid>
      <v-row
        align="start"
        justify="start"
      >
        <v-card
          class="pa-2"
          max-width="344"
          min-height="150"
          v-for="day in calendar.water_days"
          >
          <v-card-title>
            {{day.date}}
          </v-card-title>

          <v-card-text v-if="day.plants.length == 0">
            No watering today!
          </v-card-text>

          <v-card-actions v-if="day.plants.length > 0">
            <v-spacer></v-spacer>
            <v-btn
              icon
              @click="show = !show; currentDay = day"
            >
              <v-icon>{{ show && currentDay == day ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-card-actions>

          <v-expand-transition>
            <div v-show="show && currentDay == day">
              <v-divider></v-divider>
      
              <v-card-text>
                <p v-for="plant in day.plants"> {{plant.name}}</p>
              </v-card-text>
            </div>
          </v-expand-transition>
        </v-card>
      </v-row>
    </v-container>
  </v-container>
</template>

<script>
  import axios from "axios";

export default {
  name: 'HelloWorld',

  data: function() {
    return {
    calendar: {},
    currentDay: {},
    show: false
    };
  },
  created: function() {
    axios.get("api/calendars/19").then(response => {
      this.calendar = response.data;
    })
  }
};
</script>
