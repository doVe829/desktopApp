<template>
  <div id="app">
    <section v-if="firstPage" class="style container">
      <h1 class="space">Hi, wie fühlst du dich heute?</h1>
      <texts v-model="what" :title="'Was hat dich bedrückt?'"></texts>
      <texts v-model="how" :title="'Was könntest du daran ändern?'"></texts>
      <div class="mt-2">
        <h3>
          Wähle doch bitte eine Option, die deine Gesamtlaune widerspiegelt
        </h3>
        <div class="basic-grid">
          <div v-for="(mood, index) in moods" :key="index">
            <b-form-radio :value="mood.value" v-model="checked">
              {{ mood.text }}
            </b-form-radio>
          </div>
        </div>
      </div>
      <b-button
        @click="saveAsJson()"
        class="float-button"
        variant="outline-success"
      >
        Absenden!
      </b-button>
    </section>

    <!-- SECOND PAGE -->
    <section v-else class="style container">
      <h5>
        Gestern lag deine allgemeine Laune bei {{ recapMoods[0] }} und heute bei
        {{ recapMoods[1] }}.
      </h5>
      <span
        v-if="
          recapMoods[0] > recapMoods[1] ||
            (recapMoods[0] === 1 && recapMoods[1] === 1)
        "
      >
        <h3>Eine gute Sache!</h3>
      </span>
      <span v-else><h3>Mach' dir nichts draus!</h3></span>

      <div v-if="savedDataArr.length > 0">
        <p>
          Deine Laune liegt in den vergangenen {{ savedDataArr.length }} Tagen
          bei durschnittlich {{ avgValueMoods }}
        </p>
      </div>

      <h1>In Farben</h1>
      <div class="row">
        <div
          v-for="(colour, index) in colourCode"
          :key="index"
          :class="className(colour)"
          class="outline"
        >
          {{ index + 1 }}
        </div>
      </div>

      <br />
      <h1>
        Eine Übersicht deiner Eintragung:
      </h1>
      <b-button
        v-b-toggle.collapse-2
        class="m-1 float-button"
        variant="outline-info"
        >Klick mich</b-button
      >
      <b-collapse id="collapse-2">
        <div class="hyp">
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
          </div>
        </div>
      </b-collapse>
    </section>
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
      openSummary: false,
      firstPage: true
    };
  },
  computed: {
    colourCode() {
      let obj = [...this.savedDataArr];
      let x = obj.map(el => el.checkedValue.toString());

      return x;
    },
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
      return ree.toFixed(1);
    },
    moods() {
      let moodArr = [
        { text: "🙂 Es ist eigentlich ganz okay", value: 1 },
        { text: "😑 Es nervt mich schon", value: 2 },
        { text: "😱  Furchtbar", value: 3 }
      ];
      return moodArr;
    }
  },
  methods: {
    className(param) {
      if (param === "1") {
        return "green";
      } else if (param === "2") {
        return "yellow";
      } else {
        return "red";
      }
    },
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
      });

      this.what.txt = "";
      this.how.txt = "";
      this.checked = "";
      this.firstPage = false;
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
        const backToArray = newArr.map(el => JSON.parse(el));
        this.savedDataArr = backToArray;
      });
    }
  }
};
</script>

<style>
.outline {
  color: white;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.space {
  margin-bottom: 2em;
}

.basic-grid {
  display: grid;
  gap: 1rem;
  justify-items: center;

  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
}
.float-button {
  margin-top: 2em;
  border-radius: 1em;
  width: 30%;
  padding: 1em;
  text-align: center;
  background-color: white;
}
.style {
  background: #ffffff;
  color: black;
}
.hyp {
  word-break: break-all;
}
.mt-2 {
  margin-top: 8em;
  margin-bottom: 9em;
}
.button-grid {
  display: grid;
  justify-items: center;
  gap: 1em;
  grid-template-columns: repeat(auto-fit, minmax(20px, 0.5fr));
}
.green {
  height: 30px;
  width: 30px;
  background-color: #8be28b;
}
.yellow {
  background-color: #ffe561;
  height: 30px;
  width: 30px;
}
.red {
  background-color: #ff726f;
  height: 30px;
  width: 30px;
}
</style>
