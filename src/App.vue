<template>
  <v-app :dark="dark">
    <v-main
      :style="{
        backgroundColor: dark ? '#1e1e1e' : null,
        transition: 'all .1s'
      }">
      <Navbar :dark="dark" @toggle-dark="toggleDark" />
      <v-container>
        <v-card :dark="dark" flat max-width="550px" class="mx-auto">
          <h1 class="display-1 mb-4 ml-12">Turn: {{ turn ? 'X' : 'O' }}</h1>
          <v-row justify="center" no-gutters>
            <v-col
              align-self="center"
              cols="4"
              v-for="(box, i) in grid"
              :key="i">
              <VBox @click="fillBox(i)" :filled="box" />
            </v-col>
          </v-row>
        </v-card>
      </v-container>
      <v-overlay :value="loading">
        <v-progress-circular indeterminate size="64" />
      </v-overlay>
      <VWinnerOverlay
        :dark="dark"
        :value="!!winner"
        :winner="winner"
        @close="winner = ''"/>
    </v-main>
  </v-app>
</template>

<script>
import VBox from './components/VBox.vue';
import Navbar from './components/Navbar.vue';
import VWinnerOverlay from './components/VWinnerOverlay.vue';

export default {
  name: 'App',
  components: {
    VBox,
    Navbar,
    VWinnerOverlay
  },
  data() {
    return {
      grid: [],
      history: [],
      dark: false,
      loading: false,
      // True standing for X and false is for O
      turn: true,
      startWatching: false,
      winner: ''
    };
  },
  watch: {
    winner(val) {
      if (this.startWatching && !val) {
        this.initGrid();
        this.startWatching = false;
      }
    }
  },
  methods: {
    fillBox(pos) {
      if (this.grid[pos]) return;
      this.history.push(this.grid);
      this.grid[pos] = this.turn ? 'X' : 'O';
      if (this.checkWinner()) {
        this.winner = this.turn ? 'X' : 'O';
        this.startWatching = true;
      } else if (this.history.length === 10) {
        this.winner = 'None';
        this.startWatching = true;
      } else {
        this.turn = !this.turn;
      }
    },
    toggleDark() {
      this.dark = !this.dark;
      localStorage.darkMode = this.dark;
    },
    initGrid() {
      this.history = [];
      this.turn = true;
      const array = Array.from({ length: 9 });
      this.grid = array;
      this.history.push(array);
    },
    checkWinner() {
      // Horizontal row checks
      for (let i = 0; i < 9; i += 3) {
        if (
          this.grid[i] != null &&
          this.grid[i + 1] != null &&
          this.grid[i + 2] != null
        ) {
          if (
            this.grid[i] == this.grid[i + 1] &&
            this.grid[i + 1] == this.grid[i + 2]
          )
            return true;
        }
      }
      // Vertical row checks
      for (let i = 0; i < 3; i++) {
        if (
          this.grid[i] != null &&
          this.grid[i + 3] != null &&
          this.grid[i + 6] != null
        ) {
          if (
            this.grid[i] == this.grid[i + 3] &&
            this.grid[i + 3] == this.grid[i + 6]
          )
            return true;
        }
      }
      // Diagonal row checks
      if (
        this.grid[0] != null &&
        this.grid[4] != null &&
        this.grid[8] != null
      ) {
        if (this.grid[0] == this.grid[4] && this.grid[4] == this.grid[8])
          return true;
      }
      return false;
    },
    checkDarkMode() {
      if (!localStorage.darkMode) return;
      this.dark = JSON.parse(localStorage.darkMode);
    },
    prepare() {
      this.loading = true;
      this.checkDarkMode();
      this.initGrid();
      this.loading = false;
    }
  },
  mounted() {
    this.prepare();
  }
};
</script>
