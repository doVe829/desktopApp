<template>
  <div>
    <div class="container">
      <div class="row col-sm-4">
        <div v-for="(moods, index) in moodScales" :key="index">
          <input type="radio" :value="moods.value" v-model="checked" />
          <label :for="moods">{{ moods.text }}</label>
        </div>
      </div>
      <br />
      <span>Checked names: {{ checked }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "moodScale",
  props: {
    moodScales: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      checked: ""
    };
  },
  computed: {}
};
</script>
<style scoped></style>
