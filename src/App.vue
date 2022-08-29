<template>
  <h1>Realtime code with Firebase & Monaco</h1>
  <div class="flex">
    <div class="editors">
      <CodeEditor language="html" @update="(e) => (html = e)" />
      <CodeEditor language="js" @update="(e) => (js = e)" />
      <CodeEditor language="css" @update="(e) => (css = e)" />
    </div>
    <div class="preview">
      <iframe
        :srcDoc="srcDoc"
        sandbox="allow-scripts allow-downloads allow-forms allow-modals allow-pointer-lock allow-popups allow-presentation allow-same-origin allow-top-navigation-by-user-activation"
        frameborder="0"
      ></iframe>
    </div>
  </div>
</template>

<script>
import CodeEditor from "./components/CodeEditor.vue";

export default {
  components: { CodeEditor },
  data: () => ({
    html: "",
    css: "",
    js: "",
  }),
  computed: {
    srcDoc() {
      return `<html lang='fr'><body>${this.html}</body><style>${this.css}</style><script>${this.js}<\/script></html>`;
    },
  },
};
</script>

<style scoped>
.flex {
  display: flex;
}
.preview {
  width: 600px;
  height: 620px;
  box-sizing: border-box;
  border: 2px solid black;
}
.preview iframe {
  height: 100%;
  width: 100%;
  background-color: white;
}
</style>
