<template>
  <v-app>
    <!--<v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Watering Can Logo"
          class="shrink mr-2"
          contain
          src="/assets/watering_can.png"
          transition="scale-transition"
          width="40"
        />

        <b>Watering Can</b>
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>-->

    <v-content>
      <HelloWorld/>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
