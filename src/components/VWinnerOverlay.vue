<template>
  <v-dialog :dark="dark" v-model="show" max-width="400px">
    <v-card :dark="dark" max-width="400px">
      <v-card-title key="title" v-if="winner !== 'None'">Player {{winner}} win 🎉</v-card-title>
      <v-card-title key="title" v-else>This is Draw 👍</v-card-title>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: 'VWinnerOverlay',
  props: {
    winner: {
      type: String,
      required: true,
      default: '',
      validator: (val) => ['X', 'O', 'None',''].includes(val),
    },
    dark: {
      type: Boolean,
      required: true,
      default: false
    },
    value: {
      type: Boolean,
      required: true,
      default: false
    }
  },
  computed: {
    show: {
      get() {
        return this.value;
      },
      set() {
        this.$emit('close');
      }
    }
  }
};
</script>
