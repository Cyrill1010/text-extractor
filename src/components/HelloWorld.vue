<template>
  <v-container>
    <label for="recognition-image-input">Choose image</label>
    <input
      type="file"
      accept="image/jpeg, image/png"
      ref="recognition-image-input"
    /><br />
    <label for="recognition-confrefence-input">Confrefence</label>
    <input
      type="number"
      max="100"
      min="0"
      ref="recognition-confrefence-input"
      value="70"
    /><br />
    <label for="recognition-progress">File recognition progress:</label>
    <progress ref="recognition-progress" max="100" value="0">0%</progress>
    <div ref="recognition-text"></div>
    <div ref="recognition-images">
      <div ref="original-image"></div>
      <div ref="labeled-image"></div>
    </div>
  </v-container>
</template>

<script>
export default {
  // eslint-disable-next-line prettier/prettier
  data: () => ({}),
  computed: {
    file: function() {
      if (!this.$refs.recognitionImageInput.files) {
        return null;
      }
      return this.$refs.recognitionImageInput.files[0];
    },
  },
};
</script>
