<template>
  <div class="hello"></div>
</template>

<script>
import * as tf from "@tensorflow/tfjs";
import cocktailData from "../assets/complete_cocktails.json";
//import top20Ingredients from "../assets/top-20s.json";
//inputs x's = ingredients, outputs y's = categories

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  created() {
    let cocktails = [];
    let labels = [];
    let model;

    let labelList = [
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
    ];

    for (let record of cocktailData) {
      let col = [
        record.ingredientCat1,
        record.ingredientCat2,
        record.ingredientCat3,
        record.ingredientCat4,
        record.ingredientCat5,
        record.ingredientCat6
      ];
      cocktails.push(col);
      labels.push(labelList.indexOf(record.category));
    }

    //convert this to numeric using oneHot
    let xs = tf.tensor2d(cocktails);
    //tf.reshape(xs)

    let labelsTensor = tf.tensor1d(labels, "int32");
    let ys = tf.oneHot(labelsTensor, 11);
    //for memory management
    labelsTensor.dispose();

    console.log(xs.shape);
    console.log(ys.shape);
    xs.print();
    ys.print();

    model = tf.sequential();

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
    model.add(hidden);
    model.add(output);

    //create optimizer, then loss function
    //entropy is the chaos associated w/ a system - good for sigmoid + softmax
    const lr = 0.2;
    const optimizer = tf.train.sgd(lr);
    model.compile({
      optimizer: optimizer,
      loss: "categoricalCrossentropy"
    });
    //train the model
    const options = {
      epochs: 10
    };
    model.fit(xs, ys, options).then(results => {
      console.log(results);
    });
    //use the model to give a new label
  }
};
</script>
