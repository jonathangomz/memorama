<template>
  <div class="board">
    <div class="num-pairs">
      <select
        class="select-num-pairs"
        v-bind:value="num_pairs"
        v-on:change="updateBoard($event)"
      >
        <option disabled value="">Seleccione un elemento</option>
        <option
          v-for="num of max_pairs"
          v-bind:key="num - 1 + num_pairs"
          v-bind:value="num - 1 + num_pairs"
          v-bind:selected="num_pairs === num - 1 + num_pairs"
        >
          {{ num - 1 + num_pairs }}
        </option>
      </select>
    </div>
    <div class="cards">
      <template v-for="(image, index) of board" v-bind:key="index">
        <Card
          class="card"
          v-on:click="toogleActive(index)"
          v-bind:image="image"
          v-bind:isActive="selecteds[index]"
          v-bind:isHidden="hidden[index]"
        />
      </template>
    </div>
  </div>
</template>

<script>
import Card from "./Card";

const constructBoard = (images = getImagesList()) => {
  const num_cards = images.length * 2;
  const board = [];

  const getRandomIndex = (limit) => Math.floor(Math.random() * limit);

  const usedPositions = [];
  for (const image of images) {
    // First Image
    let index;
    do {
      index = getRandomIndex(num_cards);
    } while (usedPositions.includes(index));

    board[index] = image;
    usedPositions.push(index);

    // Second Image
    do {
      index = getRandomIndex(num_cards);
    } while (usedPositions.includes(index));

    board[index] = image;
    usedPositions.push(index);
  }

  return board;
};

const getImagesList = (size) =>
  Array.from(new Array(size || 8).keys()).map(
    (i) => `https://source.unsplash.com/random/200x200?sig=${i}`
  );

const validateIfAllGone = (hidden_cards) => {
  const length = hidden_cards.filter((isHidden) => !isHidden).length;

  console.log(length);

  return length === 0;
};

export default {
  data: function () {
    return {
      num_pairs: 8,
      max_pairs: 16,
      board: [],
      selecteds: [],
      hidden: [],
      selected: undefined,
      waiting: false,
    };
  },
  components: { Card },
  created() {
    const falseArray = [...new Array(this.max_pairs)].map(() => false);
    this.selecteds = [...falseArray];
    this.hidden = [...falseArray];

    this.board = constructBoard();
  },
  methods: {
    restart() {
      const falseArray = [...new Array(this.max_pairs)].map(() => false);
      this.selecteds = [...falseArray];
      this.hidden = [...falseArray];
    },
    updateBoard($event) {
      this.restart();
      this.board = constructBoard(
        getImagesList($event ? Number($event.target.value) : 8)
      );
    },
    toogleActive: function (index) {
      if (!this.waiting && !this.hidden[index]) {
        // Flip the selected card
        this.selecteds[index] = true;

        // The first index is zero and that it's a falsy value
        if (this.selected === undefined) this.selected = index;
        else {
          // Block for all the other cards
          this.waiting = true;
          setTimeout(() => {
            if (this.board[this.selected] === this.board[index]) {
              this.hidden[this.selected] = true;
              this.hidden[index] = true;
            }

            // Flip the cards
            this.selecteds[index] = false;
            this.selecteds[this.selected] = false;

            // There is no one selected again
            this.selected = undefined;
            // Unblock for all the other cards
            this.waiting = false;

            if (validateIfAllGone(this.hidden)) {
              this.board = this.constructBoard();
            }
          }, 1500);
        }
      }
    },
  },
};
</script>

<style scoped>
.num-pairs {
  margin: 18px;
}

.select-num-pairs {
  padding: 8px;
  background: transparent;
  border-radius: 5px;
  border-color: #4281A4;
  color: #4281A4;
}

.cards {
  max-width: 1024px;
  margin: auto;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
</style>