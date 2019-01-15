<template>
  <div class="container">
    <h1>MixoLogy</h1>
    <div id="lossChart" ref="lossChart"></div>
  </div>
</template>


<script>
import * as tf from "@tensorflow/tfjs";
import * as tfvis from "@tensorflow/tfjs-vis";
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
      /*labelList: [
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
      ],*/
      ingredientList: [
        "",
        "gin",
        "light rum",
        "vodka",
        "brandy",
        "sweet vermouth",
        "dark rum",
        "dry vermouth",
        "amaretto",
        "sloe gin",
        "lemon juice",
        "juice of a lime",
        "soda water",
        "bacardi rum",
        "absinthe",
        "irish whiskey",
        "bourbon whiskey",
        "scotch whiskey",
        "simple syrup",
        "blended whiskey",
        "orange bitters",
        "triple sec",
        "green creme de menthe",
        "ginger ale",
        "passion fruit syrup",
        "cranberry juice",
        "sugar",
        "orange wheel",
        "pineapple juice",
        "orange juice",
        "white creme de menthe",
        "apple cider",
        "margarita mix",
        "angostura bitters",
        "bourbon",
        "bourbon or rye whiskey",
        "kirschwasser",
        "straight rye whiskey",
        "applejack",
        "club soda",
        "mint",
        "sake",
        "anisette",
        "bitters",
        "creme de cacao (brown)",
        "creme de cacao (white)",
        "hard cider",
        "cherry-flavored brandy",
        "pear brandy",
        "hennessy v.s cognac",
        "blue curacao",
        "coffee-flavored brandy",
        "maraschino liqueur",
        "soda water or ginger ale",
        "mandarine napoleon liqueur",
        "coffee liqueur",
        "green chartreuse",
        "yellow chartreuse",
        "benedictine",
        "apple brandy",
        "creme de banana",
        "grapefruit juice",
        "cynar",
        "coconut milk",
        "tonic water",
        "juice of orange",
        "grenadine",
        "apple schnapps",
        "cola",
        "apple slice",
        "kummel",
        "tennessee whiskey",
        "orange curacao",
        "egg white",
        "whole egg",
        "chilled champagne",
        "green olive",
        "lime wedge",
        "port",
        "lemon twist",
        "sour mix",
        "light vermouth",
        "fernet-branca",
        "cinnamon schnapps",
        "half-and-half",
        "fresh carrot juice",
        "orgeat or almond syrup",
        "cream sherry",
        "apple juice",
        "light cream",
        "pimm's no 1 cup",
        "dry sherry",
        "agave nectar",
        "white whiskey",
        "lillet blanc",
        "grand mariner",
        "peach bitters",
        "aromatic bitters",
        "blackberry-flavored brandy",
        "strawberry liqueur",
        "dubonnet rouge",
        "mint syrup",
        "water",
        "egg yolk",
        "cognac",
        "canadian whiskey",
        "tequila",
        "madeira",
        "creme de cassis",
        "anis",
        "crushed or shaved ice",
        "orange twist",
        "irish cream liqueur",
        "honey syrup",
        "amaro",
        "limoncello",
        "aperol",
        "vanilla ice cream",
        "citrus-flavored vodka",
        "mezcal",
        "campari",
        "orange flavored gin",
        "jamaica rum",
        "elderflower liqueur",
        "lemon-flavored vodka",
        "pear-flavored vodka",
        "lime and salt for glass",
        "creme de noyaux",
        "grape vodka",
        "cream",
        "ginger beer",
        "worcestershire sauce",
        "cachaca",
        "light or dark rum",
        "coconut rum",
        "white chocolate liqueur",
        "lime wheel",
        "creme de violette",
        "maraschino cherry",
        "mint-flavored gin",
        "lillet rouge",
        "milk",
        "forbidden fruit",
        "vanilla syrup",
        "lime and sugar for glass",
        "roses lime juice",
        "lemon and sugar for glass",
        "lime-flavored vodka",
        "peychaud's Bitters",
        "apricot-flavored brandy",
        "orange zest",
        "edible flower",
        "cucumber",
        "celery",
        "catsup",
        "root beer",
        "manzanilla sherry",
        "lemon soda",
        "lemon-lime soda",
        "demerara syrup",
        "crema di limoncello",
        "black raspberry liqueur",
        "whole cloves",
        "grapefruit marmalade",
        "rock and rye",
        "salt",
        "pepper",
        "salt and papper",
        "pineapple",
        "maple syrup",
        "tiki bitters",
        "ground cinnamon",
        "grapefruit bitters",
        "nutmeg",
        "cream of coconut",
        "raspberry syrup",
        "lemon and salt for glass",
        "sparkling cider",
        "coffee",
        "rosemary sprig",
        "lemon wedge",
        "tea",
        "orange-flavored vodka",
        "strawberries",
        "corn whiskey",
        "cilantro leaves",
        "vanilla-infused bourbon",
        "ginger liqueur",
        "blackberry liqueur",
        "grapefruit-flavored vodka",
        "amontillado sherry",
        "vanilla liqueur",
        "clementine",
        "raspberry yogurt",
        "cherry-flavored wine",
        "cherry-flavored vodka",
        "stout",
        "strega",
        "hot shot tropical fruit liqueur",
        "strawberry schnapps",
        "peach-flavored vodka",
        "passion fruit juice",
        "vanilla-flavored vodka",
        "aquavit",
        "raspberry-flavored liqueur",
        "acai berry flavored vodka",
        "calvados",
        "pisco",
        "basil leaves",
        "red wine",
        "grape juice",
        "melon liqueur",
        "cherry liqueur",
        "peach-flavored brandy",
        "gold rum",
        "pear",
        "ginger",
        "cherry tomatoes",
        "aged rhum agricole",
        "creole shrub",
        "armagnac",
        "red grapes",
        "white wine",
        "tomato juice",
        "beer",
        "peach slice",
        "grapefruit twist",
        "fresh berries",
        "lemon wheel",
        "mango slice",
        "raspberry-flavored vodka",
        "spiced rum",
        "sage leaves",
        "thyme sprigs",
        "candy stick",
        "lychees",
        "sparkling wine",
        "banana",
        "lime twist",
        "hot sauce",
        "peppermint schnapps",
        "grapefruit soda",
        "sweet sherry",
        "swedish punch",
        "honey liqueur",
        "peach schnapps",
        "pear liqueur",
        "blood orange liqueur",
        "becherovka",
        "pomegranate syrup",
        "sambuca",
        "rose water",
        "orange blossom water",
        "lavender syrup",
        "cinnamon stick",
        "almond extract",
        "almond milk",
        "b&b",
        "vinegar",
        "pickle slice",
        "pickle brine",
        "grated chocolate",
        "apricot nectar",
        "pomegranate molasses",
        "galliano",
        "raspberry juice",
        "honey",
        "mango juice",
        "horseradish",
        "creme yvette",
        "creme de p√™che",
        "guava puree",
        "creme fraiche",
        "citrus sherbet",
        "peach liqueur",
        "punt e mes",
        "amer picon or rorani Amer",
        "v8 juice",
        "peach puree",
        "butterscotch schnapps",
        "chile syrup",
        "tomato-clam juice",
        "lemonade",
        "pastis",
        "black licorice stick",
        "pomegranate juice",
        "cherry juice",
        "cold beef bouillon",
        "tropical fruit schnapps",
        "falernum",
        "allspice liqueur",
        "olive brine",
        "drambuie",
        "collins mix",
        "mango flavored vodka",
        "mountain dew",
        "white grapes",
        "lime liqueur",
        "clam juice",
        "raspberry ice cream",
        "rhubarb syrup",
        "coconut liqueur",
        "honey-xurrant syrup",
        "ginger syrup",
        "gingerbread liqueur",
        "lychee liqueur"
      ]
    };
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
        //use these labels if you just want a general idea of the category
        //this.labels.push(this.labelList.indexOf(record.category));
      }
      for (let r of cocktailData) {
        for (let i of this.ingredientList) {
          if (r.ingredientCat1 == this.ingredientList.indexOf(i)) {
            this.labels.push(r.ingredientCat1);
          }
        }
      }
      this.oneHotEncodeLabels(this.cocktails);
    },
    oneHotEncodeLabels(cocktailLabels) {
      //convert this to numeric using oneHot
      this.xs = tf.tensor2d(cocktailLabels);

      var labelsTensor = tf.tensor1d(this.labels, "int32");
      this.ys = tf.oneHot(labelsTensor, 11);
      //for memory management
      labelsTensor.dispose();

      this.buildModel();

      /*console.log(this.xs.shape);
      console.log(this.ys.shape);
      this.xs.print();
      this.ys.print();*/
    },

    buildModel() {
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
      let LEARNING_RATE = 0.2;
      let optimizer = tf.train.sgd(LEARNING_RATE);
      this.model.compile({
        optimizer: optimizer,
        loss: "categoricalCrossentropy"
      });

      this.trainModel().then(results => {
        //console.log(results.history.loss);
        //here we will start inference with the results given
        this.getInference();

        //save the model as well
        this.model.save(`downloads://my-model`);
      });
    },

    async trainModel() {
      //train the model, 10% of training data is broken off for validation.
      let options = {
        epochs: 50,
        validationSplit: 0.1,
        shuffle: true,
        callbacks: {
          onTrainBegin: () => console.log("train start"),
          onTrainEnd: () => console.log("train end"),
          onBatchEnd: tf.nextFrame,
          onEpochEnd: async (num, logs) => {
            console.log("epoch: " + num);
            console.log("loss: " + logs.loss + "," + logs.val_loss);
            this.lossValues[0].push({ x: num, y: logs.loss });
            this.lossValues[1].push({ x: num, y: logs.val_loss });
            const lossContainer = this.$refs.lossChart;
            tfvis.render.linechart(
              { values: this.lossValues, series: ["train", "validation"] },
              lossContainer,
              {
                width: 420,
                height: 300,
                xLabel: "epoch",
                yLabel: "loss"
              }
            );
          }
        }
      };
      return await this.model.fit(this.xs, this.ys, options);
    },

    getInference() {
      //replace this with a way to choose ingredients
      let i1 = 60;
      let i2 = 2;
      let i3 = 3;
      let i4 = 4;
      let i5 = 5;
      let i6 = 70;

      tf.tidy(() => {
        const input = tf.tensor2d([[i1, i2, i3, i4, i5, i6]]);
        let results = this.model.predict(input);
        let argMax = results.argMax(1);
        let index = argMax.dataSync()[0];
        let label = this.ingredientList[index];
        console.log(index);
        this.suggestDrink(index);
      });
    },

    suggestDrink(label) {
      console.log(label);
      for (let record of cocktailData) {
        if (
          label == record.ingredientCat2 ||
          label == record.ingredientCat3 ||
          label == record.ingredientCat4
        ) {
          //this is the most likely pairing
          console.log(record);
        }
      }
    }
  },

  created() {
    this.init();
  }
};
</script>
