<template>
  <div id="app">
    <div v-if="submitted === false">
    <h1>Hi! Wie fühlst du dich heute?</h1>
    <texts v-model="what" :title="'Was hat dich bedrückt?'"></texts>
    <p>
      {{ what.txt }}
    </p>
    <texts v-model="how" :title="'Was könntest du daran ändern?'"></texts>
    <p>
      {{ how.txt }}
    </p>
    <div class="basic-grid">
      <div v-for="(mood, index) in moods" :key="index">
        <input type="radio" :value="mood.value" v-model="checked" />
        <label :for="mood">{{ mood.text }}</label>
      </div>
      <span>Checked names: {{ checked }}</span>
    </div>
    <button @click="saveData(); saveAsJson();" class="float-button" type="button"> Next page </button>

    <button type="button" @click="read"> READ STUFF </button>
    {{test}}
  </div>
  <div>
  </div>
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
      test:[],
      submitted:false,
      checked: "",

      what: {
        txt: ""
      },
      how: {
        txt: ""
      },
      savedValueArr : [],
    };
  },
  computed: {
    moods() {
      let moodArr = [
        { text: "🙂 Es eigentlich ganz okay", value: 1 },
        { text: "😑 Es nervt mich schon", value: 2 },
        { text: "😱  was soll der Kack?!", value: 3 }
      ];
      return moodArr;
    }
  },
  methods:{
    saveAsJson(){
        const fs = require('fs');
        // var app = require('electron').remote;
        // var dialog = app.dialog;
        

    
          // let map = this.savedValueArr.map((el) => el.what);
       var content = JSON.stringify(this.savedValueArr);
       fs.appendFile('testFile.txt', content, (err) =>{
           if(err) console.log(err);
           alert("File has been saved");
       })
    


    },
    saveData(){
      let summaryValues = {
        what: this.what.txt,
        how: this.how.txt,
        checkedValue: this.checked
      };
      this.savedValueArr.push(summaryValues);
      // this.submitted = true;
      
    },
    read(){
      const fs = require('fs');
        // var app = require('electron').remote;
        // var dialog = app.dialog;

    //   dialog.showOpenDialog((fileNames) => {
    // // fileNames is an array that contains all the selected
    // if(fileNames === undefined){
    //     console.log("No file selected");
    //     return;
    // }

    fs.readFile('testFile.txt', 'utf-8', (err, data) => {
        if(err){
            alert("An error ocurred reading the file :" + err.message);
            return;
        }

        // Change how to handle the file content
        console.log("The file content is : " + data);
        this.test.push(data);

    });
// });
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
.float-button{
  float: right;
  margin-top:2em;
}
</style>
