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
      <h2>{{ score }}</h2>
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
      tickDuration: null,
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
      block: null,
      cells: null,
      lastTick: Date.now(),
      score: 0,
    };
  },
  created() {
    this.root = document.querySelector(':root');
    this.style = getComputedStyle(this.root);
    this.numCols = parseInt(this.style.getPropertyValue('--num-cols'), 10);
    this.cellSize = parseInt(this.style.getPropertyValue('--cell-size'), 10);
    this.numRows = parseInt(this.style.getPropertyValue('--num-rows'), 10);
    this.tickDuration = parseInt(
      this.style.getPropertyValue('--tick-duration'),
      10
    );
    this.block = {
      shape: this.Shape.S,
      name: 'S',
      pos: [1, 3], // [row, col]
    };

    this.cells = Array(this.numRows)
      .fill(null)
      .map(() => Array(this.numCols).fill(null));
  },
  mounted() {
    this.init();
  },
  methods: {
    // 블록을 그리는 함수
    drawBlock() {
      const { name, shape, pos } = this.block;
      for (let r = 0; r < 4; r++) {
        for (let c = 0; c < 4; c++) {
          if (shape[r][c] === 0) continue;
          const row = r + pos[0];
          const col = c + pos[1];
          const cell = this.cells[row]?.[col];
          if (cell) {
            cell.classList.add(name);
          }
        }
      }
    },

    // 블록을 지우는 함수
    eraseBlock() {
      const { name, shape, pos } = this.block;

      for (let r = 0; r < 4; r++) {
        for (let c = 0; c < 4; c++) {
          if (shape[r][c] === 0) continue;
          const row = r + pos[0];
          const col = c + pos[1];
          const cell = this.cells[row]?.[col];
          if (cell) {
            cell.classList.remove(name);
          }
        }
      }
    },

    spawnBlock() {
      const name = this.Blocks[Math.floor(Math.random() * this.Blocks.length)];
      const shape = this.Shape[name];
      const pos = [-1, 3];
      const block = { name, shape, pos };
      // this.eraseBlock(); // 이전 블록 지우기

      if (this.cells[0][3].classList.contains('locked')) {
        console.log('게임 오버');
        this.gameOver();
        return;
      }

      if (block) {
        this.block = block;
      } else {
        console.error('블록 생성 실패');
      }
    },

    gameOver() {
      console.log('게임 오버');
      alert(`게임 오버! 최종 점수: ${this.score}`);

      this.score = 0; // Reset score
      this.tickDuration = parseInt(
        this.style.getPropertyValue('--tick-duration'),
        10
      ); // Reset tick duration
      this.cells.forEach((row) =>
        row.forEach((cell) => (cell.className = 'cell'))
      ); // Clear board
      this.spawnBlock(); // Spawn new block
      this.lastTick = Date.now(); // Reset last tick
      this.drawBlock(); // Draw the new block
      requestAnimationFrame(this.tick); // Restart the game loop
    },

    // 블록을 고정하는 함수
    lockBlock() {
      const { shape, pos } = this.block;
      for (let r = 0; r < 4; r++) {
        for (let c = 0; c < 4; c++) {
          if (shape[r][c] === 0) continue;
          const row = r + pos[0];
          const col = c + pos[1];
          const cell = this.cells[row]?.[col];
          if (cell) {
            cell.classList.add('locked');
          }
        }
      }
    },

    // 애니메이션을 위한 틱 함수
    tick() {
      const now = Date.now();
      if (now - this.lastTick < this.tickDuration) {
        requestAnimationFrame(this.tick);
        return;
      }
      this.lastTick = now;

      this.eraseBlock();
      const nextPos = [this.block.pos[0] + 1, this.block.pos[1]];

      if (this.canBlockMove(nextPos)) {
        this.block.pos = nextPos;
        this.drawBlock();
      } else {
        this.drawBlock(); // Re-draw at the final position
        this.lockBlock();
        this.spawnBlock();

        // Spawn new block logic here
      }

      for (let row = this.numRows - 1; row >= 0; row--) {
        let isFullRow = true;
        for (let col = 0; col < this.numCols; col++) {
          if (!this.cells[row][col].classList.contains('locked')) {
            isFullRow = false;
            break;
          }
        }
        if (isFullRow) {
          for (let r = row; r > 0; r--) {
            for (let c = 0; c < this.numCols; c++) {
              const aboveCell = this.cells[r - 1][c];
              const currentCell = this.cells[r][c];
              currentCell.className = aboveCell.className;
            }
          }
          for (let c = 0; c < this.numCols; c++) {
            this.score += 10; // Increase score
            this.cells[0][c].className = 'cell'; // Clear the top row
            if (this.score > 1000) {
              this.tickDuration = Math.max(200, this.tickDuration - 50); // Increase speed
              this.lastTick = Date.now(); // Reset last tick to avoid immediate speed change
            }
          }
        }
      }

      requestAnimationFrame(this.tick);
    },

    // 블록을 이동하는 함수
    canBlockMove(pos) {
      const { shape } = this.block;

      for (let r = 0; r < 4; r++) {
        for (let c = 0; c < 4; c++) {
          if (shape[r][c] === 0) continue;
          const row = r + pos[0];
          const col = c + pos[1];

          if (row >= this.numRows || col < 0 || col >= this.numCols) {
            return false;
          }

          const cell = this.cells[row]?.[col];
          if (!cell || cell.classList.contains('locked')) {
            return false;
          }
        }
      }
      return true;
    },

    moveBlock(direction) {
      const nextPos = [
        this.block.pos[0] + direction[0],
        this.block.pos[1] + direction[1],
      ];

      if (this.canBlockMove(nextPos)) {
        this.eraseBlock();
        this.block.pos = nextPos;
        this.drawBlock();
      }
    },

    down() {
      // 처음 블록이 생기는 위치
      const nextPos = [this.block.pos[0], this.block.pos[1]];

      // 아래로 이동 가능한지 확인
      for (let i = 0; i < this.numRows - this.block.pos[0]; i++) {
        if (!this.canBlockMove(nextPos)) {
          nextPos[0] -= 1;
          break;
        }
        nextPos[0] += 1;
      }

      if (this.canBlockMove(nextPos)) {
        this.eraseBlock();
        this.block.pos = nextPos;
        this.drawBlock();
      } else {
        this.spawnBlock();
      }
      this.lockBlock();
    },

    rotateBlock() {
      this.eraseBlock();
      const newShape = this.block.shape[0].map((val, index) =>
        this.block.shape.map((row) => row[index]).reverse()
      );

      const originalShape = this.block.shape;
      this.block.shape = newShape;

      if (!this.canBlockMove(this.block.pos)) {
        this.block.shape = originalShape; // Revert if rotation fails
      } else {
        // this.eraseBlock();
        this.drawBlock();
      }
    },

    // 테트리스 보드 초기화 함수
    init() {
      for (let row = 0; row < this.numRows; row++) {
        for (let col = 0; col < this.numCols; col++) {
          const cell = document.createElement('div');
          cell.className = 'cell';
          cell.style.gridRow = row + 1;
          cell.style.gridColumn = col + 1;
          document.getElementById('board').appendChild(cell);
          this.cells[row][col] = cell;
        }
      }

      document.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowLeft') {
          this.moveBlock([0, -1]);
        } else if (e.key === 'ArrowRight') {
          this.moveBlock([0, 1]);
        } else if (e.key === 'ArrowDown') {
          this.moveBlock([1, 0]);
        } else if (e.key === 'ArrowUp') {
          this.rotateBlock();
        } else if (e.key === ' ') {
          this.down();
        }
      });

      this.lastTick = Date.now();

      this.drawBlock(this.block);
      requestAnimationFrame(this.tick);
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
  --tick-duration: 800;
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

.I {
  background-color: hsl(0, 80%, 50%);
}
.J {
  background-color: hsl(45, 80%, 50%);
}
.L {
  background-color: hsl(90, 80%, 50%);
}
.S {
  background-color: hsl(135, 80%, 50%);
}
.O {
  background-color: hsl(180, 80%, 50%);
}
.T {
  background-color: hsl(225, 80%, 50%);
}
.Z {
  background-color: hsl(270, 80%, 50%);
}
</style>
