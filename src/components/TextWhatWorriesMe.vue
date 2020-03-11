<template>
  <div>
    <div class="container">
      <h1>{{title}}</h1>

      <textarea  rows="4" cols="50" v-model="text"> </textarea>

      <pre class="mt-3 mb-0">{{ text }}</pre>
    </div>
  </div>
</template>
<script>
export default {
  name: "TextWhatWorriesMe",
  props:{
      title:{
          type: String,
          required:true,
      }
  },
  data() {
    return {
      text: ""
    };
  },
  computed:{
  }
};
</script>
