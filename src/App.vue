<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light darken-1">Dr</span>
        <span style="color:#000f26">Rittersporn</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        text
      >
        <span class="mr-2" v-show="$vuetify.breakpoint.mdAndUp">TATTOO ARTIST | PRENZLAUER BERG</span>
      </v-btn>
    </v-app-bar>

    <v-content class="black white--text">
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
