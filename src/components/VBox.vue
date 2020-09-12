<template>
  <v-card @click="$emit('click')" outlined tile height="6rem">
    <div class="text-center">
      <h1 class="mt-4">
        {{ filled }}
      </h1>
    </div>
  </v-card>
</template>

<script>
export default {
  name: 'VBox',
  props: {
    filled: {
      type: String,
      required: true,
      default: '',
      validator: (val) => ['X', 'O', ''].includes(val)
    }
  }
};
</script>
