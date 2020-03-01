<template>
  <v-app dark>
    <div>
      <v-toolbar dense class="titlebar" color="gray">
        <v-toolbar-title>JXUtility</v-toolbar-title>

        
        <v-spacer></v-spacer>
        <v-btn icon class="cliccabile puntatore" @click.stop="minimizza">
          <v-icon>mdi-window-minimize</v-icon>
        </v-btn>
        <v-btn icon class="cliccabile puntatore" @click.stop="chiudi">
          <v-icon>mdi-window-close</v-icon>
        </v-btn>
      </v-toolbar>
      <v-content>
        <JXComponent />
      </v-content>
    </div>
  </v-app>
</template>

<script>
import JXComponent from "./components/JXComponent";
import { ipcRenderer } from "electron";
const remote = require("electron").remote;
export default {
  name: "App",

  components: {
    JXComponent
  },

  data: () => ({
    //
  }),
  methods: {
    closeApplication() {
      ipcRenderer.send("closeWindowInfo");
    },
    minimizza() {
      const window = remote.getCurrentWindow();
      window.minimize();
    },
    chiudi() {
      const window = remote.getCurrentWindow();
      window.close();
    }
  }
};
</script>
<style scoped>
.titlebar {
  -webkit-user-select: none;
  -webkit-app-region: drag;
}
.cliccabile {
  -webkit-app-region: no-drag;
}
.puntatore {
  cursor: pointer;
}

</style>
