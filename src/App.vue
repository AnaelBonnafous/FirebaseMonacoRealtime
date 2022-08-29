<template>
  <h1>Realtime code with Firebase & Monaco</h1>
  <div id="editor"></div>
</template>

<script>
import * as monaco from "monaco-editor";
import htmlWorker from "monaco-editor/esm/vs/language/html/html.worker?worker";

import { db } from "./firestore";
import { get, ref } from "firebase/database";

import { FireMonacoEditor } from "@otjs/firebase-monaco";
import { fromMonaco } from "@sagarjain0907/firepad";

export default {
  async mounted() {
    // Ajout du worker pour autocompletion de l'html
    self.MonacoEnvironment = {
      getWorker: () => new htmlWorker(),
    };

    // Récupération de la référence firebase du fichier html
    const databaseRef = (
      await get(
        ref(db, `groups/${import.meta.env.VITE_FIREBASE_GROUP_ID}/html`)
      )
    ).ref;

    // Création de l'éditeur Monaco sur l'élément <div id="editor"></div>
    const editor = monaco.editor.create(document.getElementById("editor"), {
      value: "Hello world",
      language: "html",
      theme: "vs-dark",
    });

    // Options de l'utilisateur connecté, à redéfinir dans le fichier .env
    const userOptions = {
      userId: import.meta.env.VITE_FIREBASE_USER_ID, // required
      userName: import.meta.env.VITE_FIREBASE_USER_NAME || "Anonymous",
      userColor: import.meta.env.VITE_FIREBASE_USER_COLOR || "#5CD6FF",
    };

    let realTimeEditor = null;

    //// Connexion Firebase/Monaco avec le package @otjs/firebase-monaco
    realTimeEditor = new FireMonacoEditor({
      databaseRef,
      editor,
      ...userOptions,
    });

    //// Connexion Firebase/Monaco avec le package @sagarjain0907/firepad
    // realTimeEditor = fromMonaco(databaseRef, editor, userOptions);

    console.log(realTimeEditor);
  },
};
</script>

<style scoped>
#editor {
  width: 800px;
  height: 400px;
}
</style>
