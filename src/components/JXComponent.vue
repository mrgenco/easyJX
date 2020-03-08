<template>
  <v-container fluid>
    <v-card class="mx-auto">
      <v-card-actions>
        <v-btn
          class="ma-2"
          :disabled="beautifyDisabled"
          :loading="isLoading"
          @click="beautify()"
          color="deep-purple"
        >
          Beautify
          <v-icon>mdi-{{dataFormat}}</v-icon>
        </v-btn>
        <v-btn class="ma-2" @click="copy()" v-if="text.length > 0" color="deep-purple">
          <v-icon>mdi-content-copy</v-icon>Copy
        </v-btn>
        <v-btn class="ma-2" v-if="text.length > 0" @click="clear()" color="error">Clear</v-btn>
      </v-card-actions>
    </v-card>
    <br />
    <v-textarea
      height="625"
      scrollable
      :loading="isLoading"
      v-model="text"
      :color="color"
      class="red--text"
      solo
      placeholder="Paste JSON or XML content here"
    ></v-textarea>
  
    <Prism language="javascript" >{{text}}</Prism>

    <h4 class="error--text">    {{message}}</h4>    
  </v-container>
</template>

<script>
import Prism from 'vue-prism-component'
export default {
  name: "JXComponent",
  components: {
    Prism
  },
  data: () => ({
    color: "deep-purple",
    text: "",
    json: "",
    isLoading: false,
    message: "",
    dataFormat: "",
    beautifyDisabled: true
  }),
  watch: {
    text() {
      this.validate();
    }
  },
  methods: {
    validate() {
      this.message = "";
      if (this.text === "") {
        this.color = "deep-purple";
        this.beautifyDisabled = true;
        return false;
      }

      if (this.text.startsWith("<") && this.text.endsWith(">")) {
        this.dataFormat = "xml";
        this.beautifyDisabled = false;
        return true;
      }
      try {
        this.json = JSON.parse(this.text);
        this.dataFormat = "json";
        this.beautifyDisabled = false;
        return true;
      } catch (error) {
        this.message = "The input is not a valid JSON or XML!";
        this.dataFormat = "";
        this.color = "error";
        this.beautifyDisabled = true;
        return false;
      }
    },
    beautify() {
      if (this.validate()) {
        this.isLoading = true;
        setTimeout(() => {
          try {
            if (this.dataFormat === "json") {
              this.text = JSON.stringify(this.json, null, 4);
            }

            if (this.dataFormat === "xml") {
              this.text = this.formatXML(this.text);
            }
            this.color = "deep-purple";
          } catch (error) {
            this.message = "Beautify error occured :(";
            this.color = "error";
          }

          this.isLoading = false;
        }, 500);
      }
    },
    formatXML(sourceXml) {
      var xmlDoc = new DOMParser().parseFromString(
        sourceXml,
        "application/xml"
      );
      var xsltDoc = new DOMParser().parseFromString(
        [
          // describes how we want to modify the XML - indent everything
          '<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform">',
          '  <xsl:strip-space elements="*"/>',
          '  <xsl:template match="para[content-style][not(text())]">', // change to just text() to strip space in text nodes
          '    <xsl:value-of select="normalize-space(.)"/>',
          "  </xsl:template>",
          '  <xsl:template match="node()|@*">',
          '    <xsl:copy><xsl:apply-templates select="node()|@*"/></xsl:copy>',
          "  </xsl:template>",
          '  <xsl:output indent="yes"/>',
          "</xsl:stylesheet>"
        ].join("\n"),
        "application/xml"
      );

      var xsltProcessor = new XSLTProcessor();
      xsltProcessor.importStylesheet(xsltDoc);
      var resultDoc = xsltProcessor.transformToDocument(xmlDoc);
      var resultXml = new XMLSerializer().serializeToString(resultDoc);
      return resultXml;
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