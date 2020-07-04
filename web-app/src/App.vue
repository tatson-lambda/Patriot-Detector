<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
     <h1>做個愛國香港人</h1>
      <v-spacer></v-spacer>    
    </v-app-bar>

    <v-content>
      <TensorFlow/>
    </v-content>
  </v-app>
</template>

<script>
import TensorFlow from './components/TensorFlow';

export default {
  name: 'App',

  components: {
    TensorFlow,
  },

  data: () => ({
    //
  }),
};
</script>
