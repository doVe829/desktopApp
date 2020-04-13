<template>
  <div id="app">
    <section v-if="firstPage" class="style container">
      <div class="container">
        <h1 class="mb-4">Hi, wie fühlst du dich heute?</h1>

        <texts v-model="what" :title="'Was hat dich bedrückt?'"></texts>
        <texts
          class="mt-5"
          v-model="how"
          :title="'Was könntest du daran ändern?'"
        ></texts>
        <div class="mt-5">
          <h3>
            Wähle doch bitte eine Option, die deine Gesamtlaune widerspiegelt
          </h3>
          <div class="row">
            <div v-for="(mood, index) in moods" :key="index" class="col-sm-4">
              <b-form-radio :value="mood.value" v-model="checked">
                {{ mood.text }}
              </b-form-radio>
            </div>
          </div>
        </div>
        <b-button @click="saveAsJson()" variant="outline-success" class="mt-5">
          Absenden!
        </b-button>
      </div>
    </section>

    <!-- SECOND PAGE -->
    <section v-else class="style container">
      <div v-if="typeof recapMoods[0] === 'number'">
        <h5>
          Gestern lag deine allgemeine Laune bei {{ recapMoods[0] }} und heute
          bei {{ recapMoods[1] }}.
        </h5>
      </div>
      <span
        v-if="
          recapMoods[0] > recapMoods[1] ||
            (recapMoods[0] === 1 && recapMoods[1] === 1)
        "
      >
        <h3>Eine gute Sache!</h3>
      </span>
      <span v-else-if="recapMoods[1] > 0"
        ><h3>Mach' dir nichts draus!</h3></span
      >
      <div v-if="savedDataArr.length > 0">
        <h1>
          Deine Laune liegt in den vergangenen {{ savedDataArr.length }} Tagen
          bei durschnittlich {{ avgValueMoods }}
        </h1>
      </div>
      <h3 class="mt-4 col-sm-6">Die vergangenen Tage in Farben</h3>
      <b-button
        variant="light"
        class="col-sm-2 btn-pos row"
        size="sm"
        @click="showDays = !showDays"
      >
        {{ daysOnButton }}
      </b-button>
      <div class="row">
        <div
          v-for="(colour, index) in colourCode"
          :key="index"
          :class="className(colour)"
          class="col-sm-2"
        >
          <div v-if="showDays">
            {{ index + 1 }}
          </div>
        </div>
      </div>
      <br />
      <b-button
        v-b-toggle.collapse-2
        class="m-1 float-button"
        variant="outline-info"
        >Eine Übersicht deiner Eintragung:</b-button
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
      firstPage: true,
      showDays: false
    };
  },
  computed: {
    daysOnButton() {
      if (!this.showDays) {
        return "Zeig mir die Tage!";
      } else {
        return "Versteck' die Tage";
      }
    },
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
        this.$nextTick(() => {
          this.read();
        });
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
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
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
.green {
  height: 30px;
  width: 30px;
  background-color: #8be28b;
}
.yellow {
  background-color: #fcfc4b;
  height: 30px;
  width: 30px;
}
.red {
  background-color: #ff726f;
  height: 30px;
  width: 30px;
}
</style>
