new Vue({
  el: '#app',
  vuetify: new Vuetify(),
  data: () => ({
    date: '2018-03-02',
  }),

  methods: {
    allowedDates: (val) => {
      var date = new Date(val + "Z");
      if(date.getDay() === 2){
        return true;
      } else {
        return false;
      }
      
    },
  },
})