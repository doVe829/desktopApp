<template>
  <div id="app">
    
      <h1>Hi! Wie fühlst du dich heute?</h1>
   <div class="container">
      <div class="basic-grid">
        <div  v-for="(mood, index) in moods" :key="index">
          <input type="radio" :value="mood.value" v-model="checked" />
          <label :for="mood">{{ mood.text }}</label>
        </div>
      </div>
      <br />
      <span>Checked names: {{ checked }}</span>
    </div>
    <text-what-worries-me 
    v-model="name"
    :title="'Was hat dich bedrückt?'"/>
    <text-what-worries-me 
    :title="'Was könntest du daran ändern?'"/>
    aaaaaaaaaaaaaaaaaaaaaaaaaaaa
    {{name}}
   
    

    
  </div>
</template>

<script>
import TextWhatWorriesMe from './components/TextWhatWorriesMe.vue';

export default {
  name: 'App',
  components: {
    TextWhatWorriesMe,
  },
  data() {
    return {
      checked:'',
      text:'',
      name:''

    }
  },
  computed:{
      moods() {
      let moodArr = [
        { text: "🙂 Es eigentlich ganz okay", value: 1 },
        { text: "😑 Es nervt mich schon", value: 2 },
        { text: "😱  was soll der Kack?!", value: 3 }
      ];
      return moodArr;
    },
    // selected(){
    //   let selected = '';
    //   return selected;
    // }
    
  }
}
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

.basic-grid{
  display:grid;
  gap: 1rem;

  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
}
</style>
