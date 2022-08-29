<template>
  <div :id="`${language}Editor`" class="editor"></div>
</template>

<script>
import * as monaco from "monaco-editor";

import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
import cssWorker from "monaco-editor/esm/vs/language/css/css.worker?worker";
import htmlWorker from "monaco-editor/esm/vs/language/html/html.worker?worker";
import tsWorker from "monaco-editor/esm/vs/language/typescript/ts.worker?worker";
import editorWorker from "monaco-editor/esm/vs/editor/editor.worker?worker";

self.MonacoEnvironment = {
  getWorker(_, label) {
    if (label === "json") {
      return new jsonWorker();
    }
    if (label === "css" || label === "scss" || label === "less") {
      return new cssWorker();
    }
    if (label === "html" || label === "handlebars" || label === "razor") {
      return new htmlWorker();
    }
    if (label === "typescript" || label === "javascript") {
      return new tsWorker();
    }
    return new editorWorker();
  },
};

import { db } from "../firestore";
import { get, ref } from "firebase/database";

import { fromMonaco } from "@sagarjain0907/firepad";

export default {
  props: {
    language: {
      type: String,
      default: "html",
    },
  },
  async mounted() {
    // // Récupération de la référence firebase du fichier html, css ou js
    const databaseRef = (
      await get(
        ref(
          db,
          `groups/${import.meta.env.VITE_FIREBASE_GROUP_ID}/${this.language}`
        )
      )
    ).ref;

    // // Création de l'éditeur Monaco sur l'élément <div id="{language}Editor"></div>
    const editor = monaco.editor.create(
      document.getElementById(`${this.language}Editor`),
      {
        language: this.language === "js" ? "javascript" : this.language,
        theme: "vs-dark",
      }
    );

    // // Envoie du code au rendu du composant parent à chaque modification
    editor.onDidChangeModelContent(() =>
      this.$emit("update", editor.getValue())
    );

    // // Options de l'utilisateur connecté, à redéfinir dans le fichier .env
    const userOptions = {
      userId: import.meta.env.VITE_FIREBASE_USER_ID, // required
      userName: import.meta.env.VITE_FIREBASE_USER_NAME || "Anonymous",
      userColor: import.meta.env.VITE_FIREBASE_USER_COLOR || "#5CD6FF",
    };

    // // Connexion Firebase/Monaco avec le package @sagarjain0907/firepad
    fromMonaco(databaseRef, editor, userOptions);
  },
};
</script>

<style scoped>
.editor {
  width: 600px;
  height: 200px;
}
.editor:not(.editor:first-of-type) {
  margin-top: 10px;
}
</style>
