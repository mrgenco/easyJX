<template>
  <v-container fluid>
    <v-card class="mx-auto">
      <v-card-actions>
        <v-btn
          class="ma-2"
          :loading="isLoading"
          rounded
          @click="beautify()"
          color="success"
        >Beautify</v-btn>
        <v-btn class="ma-2" rounded @click="copy()" color="primary">Copy to Clipboard</v-btn>
        <v-btn
          class="ma-2"
          v-if="text.length > 0"
          rounded
          @click="clear()"
          color="error"
        >Clear Textarea</v-btn>

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
      placeholder="Paste JSON or XML content here"
    ></v-textarea>
  </v-container>
</template>

<script>
export default {
  name: "JXComponent",

  data: () => ({
    color: "success",
    text: "",
    json: "",
    isLoading: false,
    message: ""
  }),
  methods: {
    validate() {
      this.message = "";
      if (this.text === "") {
        this.message = "The input is empty!";
        this.color = "success";
        return false;
      }

      if (this.text.startsWith("<") && this.text.endsWith(">")) {
        return true;
      }
      try {
        this.json = JSON.parse(this.text);
        return true;
      } catch (error) {
        this.message = "The input is not a valid JSON or XML!";
        this.color = "error";
        return false;
      }
    },
    beautify() {
      if (this.validate()) {
        this.isLoading = true;
        setTimeout(() => {
          try {
            this.text = JSON.stringify(this.json, null, 4);
            this.color = "success";
          } catch (error) {
            this.message = "Beautify error occured :(";
            this.color = "error";
          }

          this.isLoading = false;
        }, 500);
      }
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