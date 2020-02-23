<template>
  <v-container fluid>
    <v-card class="mx-auto">
      <v-card-actions>
        <v-btn
          class="ma-2"
          :loading="isLoading"
          tile
          outlined
          @click="beautify()"
          color="success"
        >Beautify</v-btn>
        <v-btn class="ma-2" tile outlined @click="copy()" color="primary">Copy to Clipboard</v-btn>
        <v-btn
          class="ma-2"
          v-if="text.length > 0"
          tile
          outlined
          @click="clear()"
          color="error"
        >Clear</v-btn>

        <v-spacer></v-spacer>
        <h4 class="error--text">{{message}}</h4>
      </v-card-actions>
    </v-card>

    <br />

    <v-textarea
      height="625"
      scrollable
      @change="validate()"
      :loading="isLoading"
      v-model="text"
      :color="color"
      class="red--text"
      solo
      outlined
      placeholder="Paste JSON or XML content here"
    ></v-textarea>
  </v-container>
</template>

<script>
export default {
  name: "HelloWorld",

  data: () => ({
    color: "success",
    text: "",
    json: "",
    isLoading: false,
    isValidJSON: false,
    message: ""
  }),
  methods: {
    validate() {
      this.message = "";
      try {
        this.json = JSON.parse(this.text);
        this.isValidJSON = true;
      } catch (error) {
        this.message = "The input is not a valid JSON or XML!";
        this.isValidJSON = false;
        this.color = "error";
      }
    },
    beautify() {
      this.isLoading = true;
      this.validate();
      if (this.isValidJSON) {
        try {
          this.text = JSON.stringify(this.json, null, 4);
          this.color = "success";
        } catch (error) {
          this.message = "Beautify error occured :(";
          this.color = "error";
        }
      }
      this.isLoading = false;
    },
    clear() {
      this.text = "";
      this.isLoading = false;
      this.color = "success";
      this.message = "";
    }
  }
};
</script>
<style scoped>
.custom-placeholer-color input::placeholder {
  color: rgb(27, 154, 177) !important;
  opacity: 1;
}

.custom-placeholer-color input {
  color: rgb(27, 154, 177) !important;
}
</style>