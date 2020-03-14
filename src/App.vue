<template>
  <div id="app">
    <div class="style" v-if="submitted === false">
      <h1>Hi! Wie fühlst du dich heute?</h1>
      <texts v-model="what" :title="'Was hat dich bedrückt?'"></texts>
      <p>
        <!-- DEBUG -->
        {{ what.txt }}
      </p>
      <texts v-model="how" :title="'Was könntest du daran ändern?'"></texts>
      <p>
        <!-- DEBUG -->
        {{ how.txt }}
      </p>
      <div class="basic-grid">
        <div v-for="(mood, index) in moods" :key="index">
          <input type="radio" :value="mood.value" v-model="checked" />
          <label :for="mood">{{ mood.text }}</label>
        </div>
        <!-- DEBUG -->
        <span>Checked names: {{ checked }}</span>
      </div>
      <button @click="saveAsJson()" class="float-button" type="button">
        Next page
      </button>

      <button type="button" @click="read">READ STUFF</button>
      <!-- DEBUG -->
      <!-- <div v-if="savedValueArr.length > 0">
        {{ savedValueArr }}
      </div> -->
      
      <!-- Insgesamt : {{ savedValueArr.Name.what }} -->
    </div>
    <!-- SECOND PAGE -->
    <div></div>
  </div>
</template>

<script>
// require('src/renderer.js');
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
      savedValueArr: [],
      savedDataArr :''
    };
  },
  computed: {
    summary() {
      let sum = this.savedValueArr[0];
      return sum;
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
        // Change how to handle the file content
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
</style>
