<template>
  <v-app :dark="dark">
    <v-main
      :style="{
        backgroundColor: dark ? '#1e1e1e' : null,
        transition: 'all .1s'
      }"
    >
      <Navbar :dark="dark" @toggle-dark="toggleDark" />
      <v-container>
        <v-card :dark="dark" flat max-width="550px" class="mx-auto">
          <h1 class="display-1 mb-4 ml-12">Turn: {{ turn ? 'X' : 'O' }}</h1>
          <v-row justify="center" no-gutters v-for="(row, x) in grid" :key="x">
            <v-col
              align-self="center"
              cols="3"
              v-for="(box, y) in row"
              :key="y"
            >
              <VBox @click="fillBox({ x, y })" :filled="box" />
            </v-col>
          </v-row>
        </v-card>
      </v-container>
      <v-overlay :value="loading">
        <v-progress-circular indeterminate size="64" />
      </v-overlay>
      <VWinnerOverlay :dark="dark" :value="!!winner" :winner="winner" @close="winner = ''"/>
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
    VWinnerOverlay,
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
      winner: '',
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
      if (this.grid[pos.x][pos.y]) return;
      this.history.push(this.grid);
      this.grid[pos.x][pos.y] = this.turn ? 'X' : 'O';
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
      const array = Array.from({ length: 3 }, () => Array.from({ length: 3 }));
      this.grid = array;
      this.history.push(array);
    },
    checkWinner() {
      for (let i = 0; i < 3; i++) {
        if (
          this.grid[i][0] == this.grid[i][1] &&
          this.grid[i][1] == this.grid[i][2] &&
          (this.grid[i][0] || this.grid[i][1] || this.grid[i][2]) !== undefined
        ) {
          return true;
        }
        if (
          this.grid[0][i] == this.grid[1][i] &&
          this.grid[1][i] == this.grid[2][i] &&
          (this.grid[0][i] || this.grid[1][i] || this.grid[2][i]) !== undefined
        ) {
          return true;
        }
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
