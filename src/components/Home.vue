<template>
  <div class="container">
    <line-chart
      :chartdata="chartdata"
      :options="options"/>
  </div>
</template>


<script>
import * as tf from "@tensorflow/tfjs";
import cocktailData from "../assets/complete_cocktails.json";
import LineChart from './LineChart.vue';

export default {
  name: "Home",
  components: {LineChart},
  data() {
    return {
      loaded: false,
      line: [],
      line2: [],
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
  computed: {
    chartdata() {
      return {
        datasets: [
          {
            label: "Training Data Loss",
            data: this.line,
            borderColor: "#BB00FF",
            backgroundColor: "#BB00FF"
          },
          {
            label: "Validation Data Loss",
            data: this.line2,
            borderColor: "#09B26A",
            backgroundColor: "#09B26A"
          }
        ]
      }
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
        });
        
    },

    async trainModel() {
      //train the model, 10% of training data is broken off for validation.
        let options = {
          epochs: 50,
          validationSplit: 0.1,
          shuffle: true,
          callbacks: {
            onTrainBegin: () => (console.log('train start')),
            onTrainEnd: () => (console.log('train end')),
            onBatchEnd: tf.nextFrame,
            onEpochEnd: async(num, logs) => {
              console.log('epoch: ' + num);
              console.log('loss: ' + logs.loss + "," + logs.val_loss);
              this.line.push(logs.loss)
              this.line2.push(logs.val_loss)
              
            }
          }
        }
        return await this.model.fit(this.xs, this.ys, options);
    },
    
    
  },

  created() {

    this.init();

  } 
  
};
</script>
