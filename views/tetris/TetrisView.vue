<template>
  <div id="body">
    <!-- START -->
    <h1 id="title">테트리스</h1>
    <div id="progress">
      <div></div>
    </div>
    <button id="start">start</button>
    <div id="board">
      <div class="cell"></div>
    </div>
    <div id="queue"></div>

    <div id="score">
      <h3>score</h3>
      <h2>20</h2>
    </div>

    <!-- END -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      root: null,
      style: null,
      numRows: null,
      numCols: null,
      cellSize: null,
      Blocks: [
        'I',
        'J',
        'L',
        'O',
        'S',
        'T',
        'Z', // Tetris blocks
      ],
      Shape: {
        I: [
          [0, 0, 0, 0],
          [1, 1, 1, 1],
          [0, 0, 0, 0],
          [0, 0, 0, 0],
        ],
        J: [
          [0, 0, 0, 0],
          [1, 0, 0, 0],
          [1, 1, 1, 0],
          [0, 0, 0, 0],
        ],
        L: [
          [0, 0, 0, 0],
          [0, 0, 1, 0],
          [1, 1, 1, 0],
          [0, 0, 0, 0],
        ],
        O: [
          [0, 0, 0, 0],
          [0, 1, 1, 0],
          [0, 1, 1, 0],
          [0, 0, 0, 0],
        ],
        S: [
          [0, 0, 0, 0],
          [0, 1, 1, 0],
          [1, 1, 0, 0],
          [0, 0, 0, 0],
        ],
        Z: [
          [0, 0, 0, 0],
          [1, 1, 0, 0],
          [0, 1, 1, 0],
          [0, 0, 0, 0],
        ],
        T: [
          [0, 0, 0, 0],
          [0, 1, 0, 0],
          [1, 1, 1, 0],
          [0, 0, 0, 0],
        ],
      },
    };
  },
  created() {
    this.root = document.querySelector(':root');
    this.style = getComputedStyle(this.root);
    this.numCols = parseInt(this.style.getPropertyValue('--num-cols'), 10);
    this.cellSize = parseInt(this.style.getPropertyValue('--cell-size'), 10);
    this.numRows = parseInt(this.style.getPropertyValue('--num-rows'), 10);
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      for (let row = 0; row < this.numRows; row++) {
        for (let col = 0; col < this.numCols; col++) {
          const cell = document.createElement('div');
          cell.className = 'cell';
          cell.style.gridRow = row + 1;
          cell.style.gridColumn = col + 1;
          document.getElementById('board').appendChild(cell);
        }
      }
    },
  },
};
</script>

<style lang="scss">
:root {
  --color-board: hsl(0, 0%, 15%);
  --color-bg: hsl(0, 0%, 25%);
  --num-cols: 10;
  --num-rows: 20;
  --cell-size: 25px;
}

* {
  font-family: monospace;
}

#body {
  display: grid;
  grid-template-areas:
    '. title title .'
    '. progress start .'
    '. board queue .'
    '. board score .';
  grid-template-columns: 1fr repeat(2, max-content) 1fr;
  grid-template-rows: repeat(2, max-content) 1fr max-content;
  grid-gap: 15px;
  padding-top: 15px;
}
#title {
  grid-area: title;
}

#progress {
  grid-area: progress;
  border: 8px solid gray;
  width: calc(var(--num-cols) * var(--cell-size));
  height: 40px;
  border: 8px solid var(--color-board);
  border-radius: 12px;
  background-color: var(--color-bg);
  overflow: hidden;
  > div {
    width: 33%;
    height: 100%;
    background-color: hsl(50, 90%, 50%);
  }
}

#start {
  grid-area: start;
  border: 1px solid red;
  padding: 8px 15px;
  font-size: 20px;
  border: none;
  background-color: var(--color-bg);
  border-radius: 12px;
  color: white;
  font-size: 30px;
}
#board {
  grid-area: board;
  border: 1px solid gray;

  height: 500px;
  background-color: var(--color-bg);

  border: 8px solid var(--color-board);
  border-radius: 12px;
  width: calc(var(--num-cols) * var(--cell-size));

  display: grid;
  grid-template-columns: repeat(var(--num-cols), var(--cell-size));
  grid-template-rows: repeat(var(--num-rows), var(--cell-size));

  .cell {
    .cell {
      background-color: #111;
    }
    .cell:nth-child(2n) {
      background-color: #333;
    }

    --row: 1;
    --col: 1;

    grid-row: calc(var(--row) + 1);
    grid-column: calc(var(--col) + 1);
  }
}

#score {
  grid-area: score;
  justify-self: center;
  align-items: center;
  font-size: 25px;

  > h2 {
    color: hsl(300, 80%, 50%);
    font-size: 60px;
  }
}

#queue {
  grid-area: queue;
  background-color: var(--color-bg);

  border: 1px solid gray;

  border: 8px solid var(--color-board);
  border-radius: 12px;
  display: grid;

  grid-template-columns: repeat(6, var(--cell-size));
  grid-template-rows: repeat(16, var(--cell-size));
}
</style>
