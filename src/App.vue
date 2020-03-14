<template>
  <div id="app">
    <div class="style">
      <h1>Hi! Wie fühlst du dich heute?</h1>
      <texts v-model="what" :title="'Was hat dich bedrückt?'"></texts>
      <p>
        <!-- DEBUG -->
      </p>
      <texts v-model="how" :title="'Was könntest du daran ändern?'"></texts>
      <p>
        <!-- DEBUG -->
      </p>
      <div class="basic-grid">
        <div v-for="(mood, index) in moods" :key="index">
          <input type="radio" :value="mood.value" v-model="checked" />
          <label :for="mood">{{ mood.text }}</label>
        </div>
        <!-- DEBUG -->
      </div>
      <button @click="saveAsJson()" class="float-button" type="button">
        Next page
      </button>

      <button type="button" @click="read">READ STUFF</button>
    </div>
    <!-- SECOND PAGE -->
    <div class="style">
      Gestern lag deine allgemmeine Laune bei {{ recapMoods[0] }} und heute bei
      {{ recapMoods[1] }}.
      <span v-if="recapMoods[0] > recapMoods[1]">
        Eine gute Sache!
      </span>
      <span v-else> Eine nicht so gute Sache.</span>

      <div v-if="savedDataArr.length > 0">
        Deine Laune liegt in den vergangenen {{ savedDataArr.length }} Tagen bei
        durschnittlich {{ avgValueMoods }}
      </div>
      <h1>
        Eine übersicht der Eingetragenen Werte:
      </h1>
      <button @click="openSummary = !openSummary" type="button">
        Klick mir
      </button>
      <div class="hyp" v-if="openSummary === true">
        <div
          v-for="(saves, index) in savedDataArr"
          :key="index"
          :class="index.toString()"
        >
          <h2>Tag {{ index + 1 }}</h2>
          <div>
            <h3>Was hat mich bedrückt:</h3>
            {{ saves.what }}
          </div>
          <div>
            <h3>Was könnte ich daran ändern:</h3>
            {{ saves.how }}
          </div>
          <div>
            <h3>Was war mein Gesamteindruck:</h3>
            {{ saves.checkedValue }}
          </div>
          <br />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import texts from "./components/texts.vue";

export default {
  name: "App",
  components: {
    texts
  },
  data() {
    return {
      submitted: false,
      checked: "",

      what: {
        txt: ""
      },
      how: {
        txt: ""
      },
      savedDataArr: "",
      openSummary: false
    };
  },
  computed: {
    recapMoods() {
      if (this.savedDataArr.length === 0 || this.savedDataArr === "") {
        //if second page is finished put this on next page button
        this.read();
      }
      let yesterday = [...this.savedDataArr];
      let checkedValue = yesterday.map(el => el.checkedValue);
      //returns moods of yesterday and today
      return [
        checkedValue[checkedValue.length - 2],
        checkedValue[checkedValue.length - 1]
      ];
    },
    avgValueMoods() {
      let sum = [...this.savedDataArr];
      let ree = sum.map(el => el.checkedValue);
      const reducer = (accumulator, currentValue) => accumulator + currentValue;
      ree = ree.reduce(reducer) / sum.length;
      return ree;
    },
    moods() {
      let moodArr = [
        { text: "🙂 Es eigentlich ganz okay", value: 1 },
        { text: "😑 Es nervt mich schon", value: 2 },
        { text: "😱  was soll der Kack?!", value: 3 }
      ];
      return moodArr;
    }
  },
  methods: {
    saveAsJson() {
      let summaryValues = {
        what: this.what.txt,
        how: this.how.txt,
        checkedValue: this.checked
      };
      const fs = require("fs");
      let content = "";

      content += `${JSON.stringify(summaryValues)}|`;
      fs.appendFile("test.txt", content, err => {
        if (err) console.log(err);
        alert("File has been saved");
        console.log(content);
      });

      this.what.txt = "";
      this.how.txt = "";
      this.checked = "";
    },

    read() {
      const fs = require("fs");
      fs.readFile("test.txt", "utf-8", (err, data) => {
        if (err) {
          alert("An error ocurred reading the file :" + err.message);
          return;
        }
        // getting data from text file and mutate it back to an array of objects to use again
        let dataParse = data.split("|");
        let newArr = [];
        for (let index = 0; index < dataParse.length - 1; index++) {
          newArr.push(dataParse[index]);
        }
        console.log(`newArr : ${newArr}`);
        const backToArray = newArr.map(el => JSON.parse(el));
        // newArr.map((el) => JSON.parse(el));
        console.log(backToArray);
        this.savedDataArr = backToArray;
      });
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.basic-grid {
  display: grid;
  gap: 1rem;

  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
}
.float-button {
  float: right;
  margin-top: 2em;
}
.style {
  background: #2c3e50;
  color: black;
}
.hyp {
  word-break: break-all;
}
</style>
