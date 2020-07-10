<template>
  <v-app>
    <div>
    <v-app-bar app color="primary" dense dark>
      <v-toolbar-title>做個愛國香港人</v-toolbar-title>

      <v-spacer></v-spacer>
    </v-app-bar>
    </div>
    <v-content>
      <TensorFlow />
    </v-content>
  </v-app>
</template>

<script>
import TensorFlow from "./components/TensorFlow";

export default {
  name: "App",

  components: {
   TensorFlow
  },

  data: () => ({
    //
  })
};
</script>
