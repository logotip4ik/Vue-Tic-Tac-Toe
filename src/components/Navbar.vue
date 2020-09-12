<template>
  <v-app-bar app dark :color="dark ? 'grey darken-4' : 'deep-purple'">
    <v-toolbar-title>Tic Tac Toe</v-toolbar-title>
    <v-spacer />
    <div class="text-center align-center">
      <v-btn :outlined="dark" @click="$emit('toggle-dark')"
        >{{ dark ? 'Light' : 'Dark' }} Mode</v-btn
      >
    </div>
  </v-app-bar>
</template>

<script>
export default {
  name: 'Navbar',
  props: {
    dark: {
      type: Boolean,
      required: true,
      default: false
    }
  }
};
</script>
