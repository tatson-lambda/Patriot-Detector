<template>
  <v-stepper v-model="stepperPaging" :vertical="true" :alt-labels="true">
    <v-stepper-step :complete="stepperPaging > 1" step="1">上傳圖片</v-stepper-step>

    <v-stepper-content step="1">
      <v-container>
        <v-row align="center" justify="center">
          <v-card height="400" max-width="500" class="pa-5">
            <input type="file" @change="onFileChange" />
            <img id="preview" v-if="url" :src="url" />
          </v-card>
        </v-row>
        <v-row align="center" justify="center">
          <v-col cols="12">
            <v-btn :disabled="url==null" block color="primary" @click="stepperPaging = 2">計算</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-stepper-content>

    <v-stepper-step :complete="stepperPaging > 2" step="2">結果</v-stepper-step>

    <v-stepper-content step="2">
      <v-card class="mb-12 pa-5" color="blue lighten-5" height="200px">
        <h1>大數據分析結果: {{prediction}}</h1>
        <p>
          <v-system-bar color="blue lighten-2" :style="'width:'+blue*100+'%'">
            <span style="white-space:nowrap">{{blue*100}}% 愛國</span>
          </v-system-bar>
        </p>
        <p>
          <v-system-bar color="yellow lighten-2" :style="'width:'+yellow*100+'%'">
            <span style="white-space:nowrap">{{yellow*100}}% 港獨</span>
          </v-system-bar>
        </p>
      </v-card>
      <v-container>
        <v-row>
          <v-col cols="12">
            <v-btn block color="primary" @click="stepperPaging = 1">重來</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-stepper-content>
  </v-stepper>
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
      url: null,
      stepperPaging: 1
    };
  },
  watch: {
    stepperPaging: function(val) {
      if (val == 2) {
        this.fileUploadHandler();
      }
    }
  },
  methods: {
    onFileChange(e) {
      this.file = e.target.files[0];
      this.url = URL.createObjectURL(this.file);
    },
    fileUploadHandler() {
      let img = new Image();
      img.src = URL.createObjectURL(this.file);
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
        const classNames = ["藍", "黃"];
        let dataSync = prediction.dataSync();
        this.blue = dataSync[0].toFixed(2);
        this.yellow = dataSync[1].toFixed(2);
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
  width: 75%;
  height: 75%;
  object-fit: contain;
}
</style>