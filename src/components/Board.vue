<template>
  <div class="board">
    <div class="num-cards">
      <select v-bind:value="num_pairs" v-on:change="updateBoard($event)">
        <option disabled value="">Seleccione un elemento</option>
        <option value="8">8</option>
        <option value="9">9</option>
        <option value="10">10</option>
      </select>
    </div>
    <div class="cards">
      <div class="row" v-for="row of board" v-bind:key="row">
        <Card
          class="card"
          v-for="(img, index) in row"
          v-bind:key="index"
          v-bind:image="img"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Card from "./Card";

// const getImages = () => {
//   const url = "https://picsum.photos/200";
// }

const getDivisibles = (num_cells) => {
  let last;
  const divisibles = [];
  for (let i = 2; i < num_cells; i++) {
    const result = num_cells / i;

    if (!Number.isInteger(result)) continue;
    if (!last) last = result;

    divisibles.push(i);
    if (i === last) break;
  }

  return divisibles;
};

const getRowsAndColumns = (num_cells) => {
  let divisibles = getDivisibles(num_cells);

  let rows, cols;
  if (Number.isInteger(divisibles.length / 2)) {
    const center = divisibles.slice(
      Math.floor(divisibles.length / 2) - 1,
      Math.ceil(divisibles.length / 2) + 1
    );
    rows = center[0];
    cols = center[1];
  } else {
    rows = cols = divisibles[Math.floor(divisibles.length / 2)];
  }

  return [rows, cols];
};

const constructBoard = (images) => {
  const num_cells = images.length * 2;
  const [rows, cols] = getRowsAndColumns(num_cells);
  const board = [];

  let used_cells = 0;
  for (let row of [...Array(rows).keys()]) {
    board.push([]);

    [...Array(cols).keys()].forEach(() => {
      if (used_cells < num_cells) {
        board[row].push(images[0]);

        used_cells++;
      }
    });
  }

  const getRandomRowAndColumn = (rows, cols) => [Math.floor(Math.random() * rows), Math.floor(Math.random() * cols)];
  const usedPositions = [];
  for (const image of images) {
    // First Image
    let row, col;
    do {
      [row, col] = getRandomRowAndColumn(rows, cols);
    } while (usedPositions.includes(`${row}${col}`));

    console.log('A');
    board[row][col] = image;
    console.log('B');
    usedPositions.push(`${row}${col}`);

    // Second Image
    do {
      [row, col] = getRandomRowAndColumn(rows, cols);
    } while (usedPositions.includes(`${row}${col}`));

    board[row][col] = image;
    usedPositions.push(`${row}${col}`);
  }

  return board;
};

export default {
  data: function () {
    return {
      num_pairs: 8,
      board: [],
    };
  },
  components: { Card },
  mounted() {
    this.updateBoard();
  },
  methods: {
    updateBoard($event) {
      this.num_pairs = $event ? Number($event.target.value) : 8;
      axios
        .get(`https://picsum.photos/v2/list?limit=${this.num_pairs}`)
        .then(({ data }) => data.map(({ download_url }) => download_url))
        .then((images) => (this.board = constructBoard(images)));
    },
  },
};
</script>

<style scoped>
.num-cards {
  margin: 10px;
}
.cards {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.row {
  flex-basis: 200px;
  flex-shrink: 0;

  display: flex;
  flex-direction: column;
  gap: 12px;
}
</style>