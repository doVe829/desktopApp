<template>
  <div class="date-picker container">
    <h3>{{ title }}</h3>
    <textarea
      rows="4"
      cols="50"
      ref="text"
      placeholder="Schreib' etwas..."
      :value="value.txt"
      @input="updateDate()"
    />
  </div>
</template>

<script>
export default {
  name: "texts",
  props: ["value", "title"],

  methods: {
    updateDate() {
      this.$emit("input", {
        txt: this.$refs.text.value
      });
    }
  }
};
</script>
