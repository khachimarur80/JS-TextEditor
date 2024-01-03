<template>
  <v-app>
    <TextEditor/>
  </v-app>
</template>

<script>
import TextEditor from './components/TextEditor';

export default {
  name: 'App',

  components: {
    TextEditor,
  },

  data: () => ({
    //
  }),
};
</script>
<style>
  body, html {
    margin: 0px;
    padding: 0px;
    overflow: hidden;
  }
  ::-webkit-scrollbar {
    display: none;
  }
</style>