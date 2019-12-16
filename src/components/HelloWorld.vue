<template>
  <v-container>
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <v-img
          :src="require('../assets/watering_can.png')"
          class="my-3"
          contain
          height="200"
        ></v-img>
      </v-flex>

      <v-flex mb-4>
        <h1 class="display-2 font-weight-bold mb-3">
          Welcome to Watering Can!
        </h1>
        We'll generate a calendar to help you remember when to water which plant!
      </v-flex>
    </v-layout>

    <v-container fluid>
      <v-flex>
        <v-form
        ref="form">
        <v-text-field 
        label="How many weeks for the watering calendar?"
        :rules="[rules.number]"
        v-model="params.num_of_weeks"
        ></v-text-field>
        <label>Please select your JSON file with plant data</label>
        <v-file-input 
        label="JSON Plant file" accept=".json"
        @change="onFileChange($event)"
        ></v-file-input>
        <label>Select the start date for the calendar.</label>
        <v-spacer></v-spacer>
        <v-date-picker v-model="params.start_date" :allowed-dates="allowedDates"></v-date-picker>
        <v-spacer></v-spacer>
        <v-btn @click="createCalendar">Submit</v-btn>
        </v-form>
      </v-flex>
    </v-container>

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

          <v-card-text v-if="!day.plants">
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
    show: false,
    rules: {
      number: value => /^\d+$/.test(value) || 'Please enter a number.'
    },
    params: {
      start_date: "",
      num_of_weeks: "",
      plants: {}
    }
    };
  },
  methods: {
    createCalendar: function() {
      axios.post("api/calendars", this.params).then(response => {
        this.calendar = response.data;
      })
    },
    onFileChange(e) {
      var reader = new FileReader();
      reader.onload = (fileContents) => { //onload is called when readAsText finishes
        this.params.plants = JSON.parse(fileContents.target.result); 
      };

      reader.readAsText(e);
    },
    allowedDates: (val) => {
      var date = new Date(val);
      if(date.getDay() === 0){
        return true;
      } else {
        return false;
      }
      
    },
  }
};
</script>
