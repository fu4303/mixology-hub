<template>
  <div class="container">
      <h1>MixoLogy</h1>
      <div id="lossChart" ref="lossChart"></div>
  </div>
</template>


<script>
import * as tf from "@tensorflow/tfjs";
import * as tfvis from '@tensorflow/tfjs-vis';
import cocktailData from "../assets/complete_cocktails.json";

export default {
  name: "Home",
  data() {
    return {
      loaded: false,
      lossValues: [[], []],
      options: null,
      cocktails: [],
      labels: [],
      model: [],
      xs: [],
      ys: [],
      labelList: [
        "Rum",
        "Non-alcoholic Drinks",
        "Shooters",
        "Cordials and Liqueurs",
        "Gin",
        "Brandy",
        "Tequila",
        "Whiskies",
        "Rum - Daiquiris",
        "Vodka",
        "Cocktail Classics"
      ]
    }
  },
  methods: {
    init() {
      for (let record of cocktailData) {
        let col = [
          record.ingredientCat1,
          record.ingredientCat2,
          record.ingredientCat3,
          record.ingredientCat4,
          record.ingredientCat5,
          record.ingredientCat6
        ];
        this.cocktails.push(col);
        this.labels.push(this.labelList.indexOf(record.category));
      }

      this.oneHotEncodeLabels(this.cocktails)
    },
    oneHotEncodeLabels(cocktailLabels) {
      //convert this to numeric using oneHot
    this.xs = tf.tensor2d(cocktailLabels);

    var labelsTensor = tf.tensor1d(this.labels, "int32");
    this.ys = tf.oneHot(labelsTensor, 11);
    //for memory management
    labelsTensor.dispose();

    this.buildModel();

    /*console.log(xs.shape);
    console.log(ys.shape);
    xs.print();
    ys.print();*/
    },

    buildModel(){
      this.model = tf.sequential();

        let hidden = tf.layers.dense({
          units: 16,
          activation: "sigmoid",
          inputDim: 6
        });
        //categorical distribution over K possible values
        let output = tf.layers.dense({
          units: 11,
          activation: "softmax"
        });
        this.model.add(hidden);
        this.model.add(output);

        //create optimizer, then loss function
        //entropy is the chaos associated w/ a system - good for sigmoid + softmax
        let lr = 0.2;
        let optimizer = tf.train.sgd(lr);
        this.model.compile({
          optimizer: optimizer,
          loss: "categoricalCrossentropy"
        });

        this.trainModel().then(results => {
          //console.log(results.history.loss);
          //here we will start inference with the results given
          this.getInference();
        });
        
    },

    async trainModel() {
      //train the model, 10% of training data is broken off for validation.
        let options = {
          epochs: 50,
          validationSplit: 0.2,
          shuffle: true,
          callbacks: {
            onTrainBegin: () => (console.log('train start')),
            onTrainEnd: () => (console.log('train end')),
            onBatchEnd: tf.nextFrame,
            onEpochEnd: async(num, logs) => {
              console.log('epoch: ' + num);
              console.log('loss: ' + logs.loss + "," + logs.val_loss);
              this.lossValues[0].push({'x': num, 'y': logs.loss});
              this.lossValues[1].push({'x': num, 'y': logs.val_loss});
              const lossContainer = this.$refs.lossChart;
              tfvis.render.linechart(
                  {values: this.lossValues, series: ['train', 'validation']}, lossContainer,
                  {
                    width: 420,
                    height: 300,
                    xLabel: 'epoch',
                    yLabel: 'loss',
                  });
            }
          }
        }
        return await this.model.fit(this.xs, this.ys, options);
    },

    getInference(){
      //replace this with a way to choose ingredients
      let i1 = 1;
      let i2 = 2;
      let i3 = 3;
      let i4 = 4;
      let i5 = 5;
      let i6 = 70;

      tf.tidy(() => {
        const input = tf.tensor2d([
          [i1,i2,i3,i4,i5,i6]
        ]);
        let results = this.model.predict(input);
        let argMax = results.argMax(1);
        let index = argMax.dataSync()[0];
        let label = this.labelList[index];
        console.log(label)
      })
    }
    
    
  },

  created() {

    this.init();

  } 
  
};
</script>
