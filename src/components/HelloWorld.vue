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


    <v-container fluid v-if="!calendarCreated" class="form">
      <transition name="slide-right" mode="out-in">
        <v-form
        ref="form" v-if="step==1" v-bind:key="1">
          <label>How many weeks for the watering calendar?</label>
          <v-text-field 
          label="Number of Weeks"
          :rules="[rules.number]"
          v-model="params.num_of_weeks"
          ></v-text-field>
        </v-form>
        <v-form
        ref="form" v-if="step==2" v-bind:key="2">
          <label>Please select your JSON file with plant data</label>
          <v-file-input 
          label="JSON Plant file" accept=".json"
          @change="onFileChange($event)"
          ></v-file-input>
        </v-form>
        <v-form
        ref="form" v-if="step==3" v-bind:key="3">
          <label>Select the start date for the calendar.</label>
          <v-spacer></v-spacer>
          <v-date-picker v-model="params.start_date" :allowed-dates="allowedDates"></v-date-picker>
          <v-spacer></v-spacer>
        </v-form>
      </transition>
      <v-btn class="button" @click="step -= 1" v-if="step != 1">Back</v-btn>
      <v-btn class="button" @click="next()" v-if="step < 3">Next</v-btn>
      <v-btn class="button" @click="createCalendar" v-if="step == 3">Submit</v-btn>
    </v-container>

    <!-- <v-container fluid v-if="!calendarCreated">
      <v-flex>
        <v-form
        ref="form" class="form">
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
      <v-btn @click="calendarCreated = true" id="">Close</v-btn>
    </v-container> -->

    <v-container fluid v-if="calendarCreated">
      <v-btn @click="calendarCreated = false">New Calendar</v-btn>
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
              @click="currentDay == day ? currentDay = {} : currentDay = day"
            >
              <v-icon>{{ currentDay == day ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-card-actions>

          <v-expand-transition>
            <div v-show="currentDay == day">
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
    calendarCreated: false,
    currentDay: {},
    step: 1,
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
      if(this.params.start_date) {
          axios.post("api/calendars", this.params).then(response => {
          this.calendar = response.data;
          this.calendarCreated = true;
          this.step = 1;
        })
      } else {
        window.alert("Please select a start date.");
      }
      
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
    next() {
      switch(this.step) {
        case 1:
          if(!this.params.num_of_weeks) { 
            window.alert("Please select a number of weeks for the calendar."); 
          } else {
            this.step += 1;
          }
          break;
        case 2:
          if(!Array.isArray(this.params.plants)) { 
              window.alert("Please upload a plants information file."); 
            } else {
              this.step += 1;
            }
            break;
      }
    }
  }
};
</script>
