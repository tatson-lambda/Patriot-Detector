<template>
  <v-container>
    <v-row>
      <v-col>
        <input type="file" @change="onFileChange" />

        <div>
          <img id="preview" v-if="url" :src="url" />
        </div>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-btn type="file" center>Upload</v-btn>
        <p>You are a {{prediction}}</p>
        <p>{{blue}}% Blue</p>
        <p>{{yellow}}% Yellow</p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import * as tf from "@tensorflow/tfjs";

export default {
  data: function() {
    return {
      file: null,
      blue: 0,
      yellow: 0,
      prediction: "",
      model: null,
      url: null
    };
  },
  methods: {
    onFileChange(e) {
      const file = e.target.files[0];
      this.url = URL.createObjectURL(file);
      this.fileUploadHandler(file);
    },
    fileUploadHandler(url) {
      let img = new Image();
      debugger
      img.src = URL.createObjectURL(url);
      img.onload = async () => {
        const a = tf.browser.fromPixels(img);
        let offset = tf.scalar(127.5);
        let a2 = a
          .sub(offset)
          .div(offset)
          .expandDims();
        const aaa = tf.image.resizeBilinear(a2, [224, 224]);
        console.log(aaa.arraySync());
        //const aa = tf.stack([aaa])
        const aa = aaa;
        const prediction = await this.model.predict(aa);
        console.log(prediction.dataSync());
        const classNames = ["blue", "yellow"];
        let dataSync = prediction.dataSync();
        this.blue = dataSync[0];
        this.yellow = dataSync[1];
        this.prediction = classNames[tf.argMax(prediction, 1).dataSync()];
      };
    }
  },
  mounted: async function() {
    //init script
    this.model = await tf.loadLayersModel("../tf/model.json");
  }
};
</script>

<style scoped>
#preview {
  width: 500px;
  height: 500px;
  object-fit: contain;
}
</style>